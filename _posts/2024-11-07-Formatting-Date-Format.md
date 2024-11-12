---
title: GitHub Blog - 날짜 형식 포맷팅하기
date: 2024-11-07
tags: [Blogging, Git, Jekyll]
categories: [GitHub Blog]
comments: true
---

> 해당 블로그는 [jekyll-theme-chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) 테마를 기준으로 작성하였습니다.

## 서론
---
GitHub 블로그를 운영하다보면 언제 발행되었는지를 알 수 있는 글의 발행 날짜가 중요한 요소로 작용합니다. 글이 언제 작성되었는지, 글이 최신 정보인지 쉽게 파악할 수가 있죠.

하지만 기본 제공되는 날짜 형식이 맘에 들지 않거나, 날짜 형식을 **`YYYY-mm-dd`**형식처럼 한국인이 보기 편하게 바꾸고 싶을 때가 있을 수 있습니다.

특히 `jekyll` 기반의 GitHub 블로그에서는 설정 파일이나 코드 내에서 날짜 포맷을 직접 조정할수가 있기 때문에 원하는 형식으로 변경할 수 있습니다.

이번 글에서는 GitHub 블로그에서 날짜 형식을 포맷팅하는 방법을 다뤄보겠습니다.

## 날짜 형식 포맷팅
---
`jekyll`기반의 블로그라면 `Nov 8, 2024`처럼 기본으로 제공되는 날짜 형식으로 보이실 겁니다.
![날짜포맷팅1](/assets/img/formatting-Date-Format-img/날짜포맷팅1.png)
`Nov 8, 2024`의 날짜 형식을 `YYYY-mm-dd`형식으로 변경해 보겠습니다.

지금 사용하는 언어의 파일로 이동해 코드 몇줄만 바꿔주시면 됩니다. 

본인의 `github.io`폴더로 이동하고 `_data/locales/`경로에 `en.yml`파일을 보시면 `df`속성이 있을겁니다.
```yaml
df:
  post:
    strftime: "%b %e, %Y"
    dayjs: "ll"
  archives:
    strftime: "%b"
    dayjs: "MMM"
```
`strftime`가 바로 `jekyll`에서 사용하는 날짜 포맷 형식인데요. `en`을 사용하시는 분들은 기본적으로 `월 일, 연도`처럼 표시됩니다. 저 부분을 아래 코드처럼 변경합니다.
```yaml
df:
  post:
    strftime: "%Y-%m-%d"
    dayjs: "YYYY-MM-DD"
```
이후 로컬서버를 가동하여 확인합니다.
```shell
jekyll serve
```

home과 post부분의 날짜 형식이 바뀐것을 볼 수 있습니다.
- post
![날짜포맷팅2](/assets/img/formatting-Date-Format-img/날짜포맷팅2.png)
- home
![날짜포맷팅3](/assets/img/formatting-Date-Format-img/날짜포맷팅3.png)

## 마무리
---
저는 `Nov 8, 2024`형식의 날짜가 보기 불편하여 한눈에 파악하기 쉽게 `YYYY-mm-dd`형식으로 변경했기 때문에 가독성이 더 높아진 것 같습니다. 

날짜 포맷팅을 원하신 분들에게 도움이 되었기를 바랍니다.

---

## GitHub블로그 커스텀하기 시리즈
---
[GitHub Blog - 댓글 기능 추가하기](https://gnlwo021.github.io/posts/%EB%8C%93%EA%B8%80_%EA%B8%B0%EB%8A%A5_%EC%B6%94%EA%B0%80/)