---
title: "[React] SEO 최적화에 대해 Link가 유리할까? useNavigate이 유리할까?"
date: 2024-12-11
tags: [React, SEO]
categories: [React, SEO]
comments: true
---

## 리액트에서 페이지이동
---
리액트에서 페이지를 이동하는데에 2가지 방법이 있습니다. 하나는 `<Link>`태그이고, 다른 하나는 `useNavigate()`입니다. 이 두가지 페이지를 이동하는 방법에 대해 알아 보도록 하겠습니다.

## Link
---
`Link`는 React Router 또는 Next.js에서 제공하는 컴포넌트로, HTML의 `<a>` 태그와 유사하게 작동합니다.

### Link 특징
---
- HTML의 `<a>`태그로 변환하여 검색 엔진 크롤러가 쉽게 URL간의 관계를 이해하기 때문에 **<font color="#1E90FF">SEO에 최적화</font>**된 구조를 유지할 수 있음.
- **<font color="#1E90FF">브라우저 전체를 리로드하지 않고</font>**, React Router의 라우팅을 통해 컴포넌트만 업데이트 하여 페이지 새로고침을 방지함.
- 메뉴, 내비게이션 바, 하단 링크 같이 **<font color="#1E90FF">정적 라우팅</font>**에 적합함.

```javascript
import { Link } from 'react-router-dom';

export default function LinkEx() {
    return (
        <nav>
            <ul>
                <li><Link to="/about">About</Link></li>
                <li><Link to="/services">Services</Link></li>
                <li><Link to="/contact">Contact</Link></li>
            </ul>
        </nav>
    );
}
```
<blockquote class="prompt-tip"><code class="highlighter-rouge">Link</code>태그에 <code class="highlighter-rouge">to</code>사용하여 페이지간 이동 가능합니다.</blockquote>

### Link 작동방식
---
브라우저는 `<a href="/about">`로 렌더링하며, React Router는 URL 변경 시 해당 경로에 매핑된 컴포넌트를 로드합니다.

## useNavigate
---
`useNavigate`는 React Router의 훅으로, 프로그래밍 방식의 내비게이션을 구현합니다. 즉, 버튼 클릭, 이벤트 처리와 같이 동적인 상호작용에 따라 라우팅을 수행합니다.

### useNavigate 특징
---
- 프로그래밍 방식의 네비게이션을 사용하여 **<font color="#1E90FF">사용자의 특정 동작</font>**에 따라 페이지를 이동함.
- **<font color="#1E90FF">URL 매개변수를 포함</font>**한 경로 이동이나 특정 조건에 따른 이동에 유용.
- 순수하게 JavaScript로 라우팅을 처리.

```javascript
import { useNavigate } from 'react-router-dom';

export default function ButtonNavigation() {
    const navigate = useNavigate();

    const handleClick = () => {
        navigate('/about');
    };

    return (
        <button onClick={handleClick}>페이지 이동</button>
    );
}
```
### useNavigate 작동방식
---
버튼 클릭 시 `navigate('/about')`를 호출하여 JavaScript로 경로를 변경하고 React Router가 해당 컴포넌트를 로드합니다.

## Link와 useNavigate의 차이
---
`Link`와 `useNavigate`의 차이는 다음과 같습니다.

|항목|Link|useNavigate|
|------|---|---|
|**사용 방식**|HTML `<a>`태그처럼 작동|JavaScript로 경로를 변경|
|**SEO 친화도**|높음|낮음|
|**HTML 태그 생성 여부**|`<a>`태그로 렌더링|HTML 태그 생성 안 함|
|**적합한 적용 사례**|소메뉴, 네비게이션 바, 정적 링크|동적 이벤트|
|**검색 엔진 색인**|가능|제한적|
|**라우팅 방식**|정적 라우팅에 적합|동적 라우팅에 적합|

## Link와 useNavigate을 어느 상황에 써야할까?
---

### Link를 사용해야 하는 경우
---
- 검색 엔진이 페이지를 **<font color="#1E90FF">색인</font>**할 수 있어야 하는 경우.
- 네비게이션 메뉴, 사이트맵, 하단 링크 같은 곳에 사용하면 **<font color="#1E90FF">SEO에 유리</font>**할 수 있음.
- 페이지가 사용자 **<font color="#1E90FF">상호작용 없이</font>** 링크 클릭만으로 이동 가능할 때.

### useNavagate를 사용해야 하는 경우
---
- 사용자 동작에 따라 **<font color="#1E90FF">조건부</font>**로 경로를 이동해야 하는 경우
- 로그인 성공 후 홈으로 이동하는 페이지같은 **<font color="#1E90FF">동적 상호작용</font>**이 필요한 경우에 적합.

```javascript
const navigate = useNavigate();
const handleItemClick = (id) => navigate(`/items/${id}`);
```

## 그러면 SEO에는 어떤게 더 유리할까?
---
앞서 언급했듯이, `Link`는 검색 엔진 크롤러가 링크를 쉽게 색인할 수 있는 HTML의 `<a>`태그를 사용하고, 브라우저의 기본 동작을 유지하기 때문에 크롤러가 링크를 따라갈 수가 있어서 **`Link`**가 SEO에 적합하다고 할 수 있습니다.

반대로 SEO가 필요 없는 페이지면 JavaScript를 의존하는 useNavigate가 적합합니다.

## 요약
---
- `Link`: SEO가 중요하거나 정적 네비게이션이 필요한 경우 사용
- `useNavigate`: 동적 이벤트에 따라 라우팅해야 할 때 사용