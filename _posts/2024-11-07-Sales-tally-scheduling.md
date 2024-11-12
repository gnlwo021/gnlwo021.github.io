---
title: 매출 집계 스케줄링 & upsert해보기
date: 2024-11-07
tags: [JPA, PostgreSQL]
categories: [JPA]
comments: true
published: false # 임시숨기기
---

이번 포스팅에서는 JPA를 활용해 매출 데이터를 효율적으로 집계하고, 스케줄링을 통해 이를 자동화하는 방법을 다루고자 합니다. 특히, 데이터베이스에 이미 존재하는 데이터는 업데이트하고, 없는 데이터는 삽입하는 Upsert(업서트) 방식을 살펴보겠습니다.

## Upsert란?
단어에서 힌트가 있듯이, `insert`와 `update`의 합성어입니다. **`upsert`**는 주어진 조건에 따라 데이터를 삽입하거나, 이미 존재하면 업데이트하는 작업을 수행합니다.
> - `insert`: 데이터가 데이터베이스에 존재하지 않는 경우, 새 데이터를 삽입합니다.
- `update`: 데이터가 이미 존재하는 경우, 기존 데이터를 업데이트합니다.

### upsert - SQL(PostgreSQL)의 경우

> `PostgreSQL`에서 `upsert`는 `ON CONFLICT DO UPDATE` 구문을 사용하여 구현합니다. 위 SQL에서 삽입하려는 데이터가 중복되면(예: prod_id 같은 고유 키가 충돌할 때) 새로운 데이터로 기존 데이터를 업데이트 합니다.

```sql
INSERT INTO products (prod_id, prod_name, pay, prod_typd_cd, reg_dttm)
VALUES (1, 'Apple', 1300, 'fut', CURRENT_TIMESTAMP)
ON CONFLICT (prod_id) 
DO UPDATE SET 
    prod_name = EXCLUDED.prod_name, 
    prod_typd_cd = EXCLUDED.prod_typd_cd;
```

- `ON CONFLICT (prod_id)`: `prod_id` 열에서 충돌(중복)이 발생하면 아래 `DO UPDATE` 절이 실행됩니다.
    - 이 과정에서 `prod_name`과 `prod_type_cd`는 각각 새로운 값으로 업데이트 됩니다.
- **충돌(중복)**이 발생하지 않으면 업데이트 하지않고 새로운 데이터가 추가됩니다.
- `EXCLUDED`는 `ON CONFLICT`에서 새로 삽입하려는 값(즉, 충돌이 발생한 새 값)을 참조하는 키워드입니다.

### upsert - JPA의 경우

> JPA의 EntityManager를 사용하여 네이티브 SQL 쿼리를 실행하는 방식으로, 특정 기간 동안 조건에 맞는 데이터를 INSERT하거나 이미 존재하는 경우 UPDATE하는 Upsert를 구현한 예시입니다.

```java
	...
@Autowired
private EntityManager entityManager;

public void insertProducts(Long prodId, String startDate, String endDate) {
      String sql = "INSERT INTO products(prod_name, pay, prod_type_cd) \n"+
          "SELECT prod_id, prod_name, pay, prod_type_cd \n"+
          "FROM products \n"+
          "WHERE prod_id = :prodId \n"+
          "AND reg_dttm > TO_DATE(:startDate, 'YYYY-MM-DD HH24:MI:SS') \n"+
          "AND reg_dttm < TO_DATE(:endDate, 'YYYY-MM-DD HH24:MI:SS') \n"+
          "ON CONFLICT (prod_id) \n"+
          "DO UPDATE \n"+
          "SET prod_name = excluded.prod_name, prod_type_cd = excluded.prod_type_cd;";

      entityManager.createNativeQuery(sql)
      		.setParameter("prod_id", prodId)
      		.setParameter("startDate", startDate)
            .setParameter("endDate", endDate)
            .executeUpdate();
}
  	...
```
- `products` 테이블에서 조건에 맞는 데이터를 조회하여 `prod_id` 기준으로 중복 시 삽입하거나 업데이트하는 Upsert 쿼리를 실행합니다.
- `startDate`와 `endDate` 범위 조건을 만족하는 데이터를 `TO_DATE`로 변환하여 쿼리의 파라미터로 설정합니다.
- `EntityManager`를 사용해 네이티브 SQL 쿼리를 실행하여 데이터베이스에 변경 사항을 반영합니다.

### QueryDSL로 Upsert구현할 수 있을까?
그렇다면 QueryDSL로 Upsert를 구현할 수 있을까요?

결론은 "NO"입니다.

QueryDSL로 Upsert를 구현하지 못하는 이유는 2가지 이유가 있습니다.
- QueryDSL이 JPA 및 SQL의 추상화된 쿼리 작성 도구이기 때문입니다.
- QueryDSL 특성상 JPA, Hibernate와 같은 ORM 프레임워크 위에서 동작하고, 이 프레임워크들은 데이터베이스와 독립적인 방식으로 데이터 조작을 처리하려고 하기 때문입니다.

이러한 이유들 때문에 JPA로 `upsert`를 구현하기 위해서는 네이티브 쿼리를 이용하는 편이 가장 좋다고 생각합니다.

위에서 구현한 `upsert`를 스케줄링으로 돌려 주기적으로 업데이트가 가능하게 하겠습니다.

## 스케줄링

먼저, 스케줄링을 사용하기 전에 스케줄링을 사용하기 위한 어노테이션을 알아봅시다.

### @Scheduled
`@Scheduled`는 Spring Framework에서 제공하는 어노테이션으로, 특정 메서드를 정해진 일정에 따라 자동으로 실행되도록 스케줄링할 때 사용됩니다. 보통 배치 작업, 정기적인 데이터 업데이트, 자동화된 작업 등에 유용합니다.

### @Scheduled 속성들
`@Scheduled(...)`에서 ...에 들어가는 속성은 4가지가 있습니다.
- `fixedRate`: 설정된 시간 간격(밀리초)마다 메서드를 반복 실행합니다. (예시: @Scheduled(fixedRate = 5000) → 5초마다 실행)
- `fixedDelay`: 이전 작업이 완료된 후 설정된 시간(밀리초) 후에 다음 작업을 실행합니다. (예시: @Scheduled(fixedDelay = 5000) → 이전 작업이 끝난 후 5초 후에 실행)
- `initialDelay`: 처음 실행할 때만 설정된 시간(밀리초) 후에 작업을 시작합니다. (예시: @Scheduled(fixedDelay = 5000, initialDelay = 10000)→ 10초 후 처음 실행, 이후에는 5초 간격으로 실행)
- **`cron`**: 복잡한 일정(예: 매일 특정 시간에 실행)을 설정합니다. (예시: @Scheduled(cron = "0 0 12 * * ?") → 매일 12시에 실행)

만약, `cron`을 사용한다면 아래 코드처럼 사용할 수 있습니다.
```java
...
    @Scheduled(cron = "0 1 0 1 * ?") // 매월 자정 1분
    @Transactional
    public void productUpsert(Long prodId) {

        String startDate = DateUtil.getBeforeMonthDay("first");
        String endDate = DateUtil.getNowMonthFirstDay();
        prodService.insertProducts(prodId, startDate, endDate);
    }
...
```
> `startDate`는 해당 달의 첫번째 날이고, `endDate`는 해당 달의 마지막 날입니다.

위 코드를 실행하면 매월 자정 1분에 `upsert`로직이 실행됩니다.