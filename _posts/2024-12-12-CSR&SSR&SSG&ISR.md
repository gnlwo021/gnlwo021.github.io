---
title: "CSR & SSR & SSG & ISR 알아보기"
date: 2024-12-12
tags: [React, Next.js, Redering]
categories: [SEO]
comments: true
---

## 서론
---
React로 프로젝트를 진행하던 중, 구글에서 내 사이트를 검색결과에 표시되어야 하는 작업을 맡았었습니다. 그러다가 SEO에 대해서 알게 되었고, SEO 최적화를 잘 하면 구글 검색결과 상위에 표시된다는 것을 알게되었습니다. 그래서 SEO에 중요한 CSR, SSR, SSG, ISR에 대해서 알아보겠습니다.

## CSR(Client Side Rendering)
---
`CSR(Client Side Rendering)`은 브라우저에서 JavaScript를 실행해 화면을 렌더링하는 방식입니다. HTML문서는 기본적으로 비어 있고, JavaScript가 로드된 후 브라우저가 화면을 완성합니다.

쉽게 말해, **<font color="#1E90FF">클라이언트에서 렌더링</font>**하는 방식을 말하고, 이 CSR방식을 사용하는 대표적인 웹 프레임워크가 바로 `React`입니다.

### CSR 동작 과정
---
사용자가 최초에 페이지를 들어가면 `index.html`에 **<font color="#1E90FF">모든 페이지를 포함한 파일을 번들링</font>**한 후 `index.html`파일을 다운받습니다.

첫 페이지를 로딩 할 때, 번들링된 큰 파일을 받아온 후 화면에 렌더링 해야하기 때문에 로딩이 많이 느립니다. `index.html`파일은 **<font color="#1E90FF">빈 파일</font>**이 이기 때문에 **<font color="#1E90FF">SEO에 불리</font>**합니다.

이후, 번들링 된 큰 파일에서 JavaScript를 실행시켜 화면을 생성합니다. 파일의 용량이 커서 로딩이 느리지만 `index.html`파일에 모든페이지의 JavaScript파일이 있기 때문에 다른 페이지로 이동 시 **<font color="#1E90FF">새로고침없이 이동이 가능</font>**합니다.

화면이 생성되고, API호출이 있다면 클라이언트쪽에서 요청을 보내 데이터를 가져온 후 화면에 렌더링됩니다. 여기서 API호출을 클라이언트에서 보내기 때문에 **<font color="#1E90FF">보안에 좋지 않습니다.</font>**

CSR 동작과정을 요약하자면,

- 최초 페이지 진입 시 번들링 된 `index.html`파일을 다운받음과 동시에 사용자는 그동안 빈 화면을 보게 됨
- 화면이 생성되고 난 후 API요청으로 응답받은 데이터를 화면에 렌더링

아래와 같이 간단한 예시를 보겠습니다.
```javascript
import React, { useState, useEffect } from 'react';

function App() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch('/api/data')
      .then((response) => response.json())
      .then((result) => setData(result));
  }, []);

  return <div>{data ? data.message : 'Loading...'}</div>;
}

export default App;
```
위 예시는 `React`에서 사용자가 버튼을 클릭하면 데이터를 API에서 가져와 화면에 표시하는 간단한 예시입니다.

![CSR](/assets/img/CSR&SSR&SSG&ISR/csr.png)

### CSR 장점
---
- `SPA`방식으로 클라이언트에서 모든 상태를 관리하여 **<font color="#1E90FF">빠른 상호작용</font>**이 가능
- API를 통해 데이터를 실시간으로 가져오고 업데이트하여 **<font color="#1E90FF">동적 데이터 처리에 특화</font>**
- 정적인 HTML과 JavaScript만 제공하기 때문에 **<font color="#1E90FF">서버 부하가 감소</font>**

### CSR 단점
--- 
- 검색 엔진 크롤러가 JavaScript를 실행하지 못하면 콘텐츠를 인덱싱할 수 없기때문에 **<font color="#1E90FF">SEO에 비효율적</font>**
- JavaScript를 다운로드하고 실행한 후에야 화면을 렌더링하기 때문에 **<font color="#1E90FF">초기 로딩 속도가 느림</font>**

## SSR(Server-Side Rendering)
---
SSR방식은 브라우저 요청 시 서버에서 HTML을 동적으로 생성해 제공하는 방식입니다.

쉽게 말해, SSR은 `HTML`파일을 **<font color="#1E90FF">서버에서 생성</font>** 한다는 뜻입니다. 대표적으로 `Next.js`가 SSR방식을 사용하고 있습니다.

### SSR 동작 과정
---
각 페이지 진입 시 서버가 `HTML`파일을 생성하고 JavaScript파일을 다운로드 받습니다. 이 과정은 페이지를 진입할 때 마다 그때그때 `HTML`파일을 **<font color="#1E90FF">동적으로 생성</font>**합니다.

이후, 서버에서 브라우저에게 `HTML`파일만 전달하면 브라우저는 쉽게 렌더링 할 수 있습니다.

SSR 동작과정을 요약하자면,

- 브라우저가 요청을 보내면 서버가 HTML을 동적으로 생성
- 서버에서 생성된 HTML이 브라우저에 전달
- 브라우저는 HTML을 렌더링하고, 필요한 추가 데이터는 JavaScript로 처리

아래와 같이 간단한 예시를 보겠습니다.
```javascript
export async function getServerSideProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();

  return { props: { data } };
}

function Page({ data }) {
  return <div>{data.message}</div>;
}

export default Page;
```
`Next.js`에서 서버에서 데이터를 가져와 HTML을 생성한 뒤 클라이언트에 전송하는 간단한 예시입니다.

![SSR](/assets/img/CSR&SSR&SSG&ISR/ssr.png)

### SSR 장점
---
- 완전한 `HTML`을 서버에서 제공하므로 검색 엔진이 쉽게 인덱싱이 가능하여 **<font color="#1E90FF">SEO 최적화</font>**에 효율적
<blockquote class="prompt-tip">SEO 최적화가 잘되어 있으면 구글 검색 시 상위에 표시될 확률이 높아집니다.</blockquote>

- 요청 시 데이터를 가져와 렌더링하므로 **<font color="#1E90FF">항상 최신 상태를 유지</font>**

### SSR 단점
---
- 모든 요청마다 서버에서 HTML을 생성하므로 서버에 부하가 갈 수 있음
- 서버에서 HTML을 생성하는 데 시간이 걸림
- 페이지를 이동할 때 마다 새로고침이 됨

## SSG(Static Site Generation)
--- 
SSG는 **<font color="#1E90FF">빌드 시점에 HTML파일을 생성</font>**해 정적 파일로 배포하는 방식으로, 브라우저는 준비된 HTML 파일을 그대로 받아 렌더링하는 방식입니다.

쉽게 말해, **<font color="#1E90FF">빌드시점에 미리 페이지를 렌더링</font>**한다는 뜻입니다.

### SSG 동작과정
---
사용자가 페이지 진입 시 필요한 데이터를 가져와 정적인 `HTML`파일을 생성합니다. 여기서 정적인 `HTML`파일을 생성하기 때문에 서버 부하 없이 빠르게 전달 할 수 있습니다.

이후 JavaScript 또는 CSS 파일이 로드되어 페이지가 완전히 렌더링됩니다.

SSG의 동작과정을 요약하자면,

- SSG는 빌드 시 데이터를 미리 가져와 HTML 파일을 생성
- 사용자가 요청할 때 서버 작업 없이 정적 파일을 바로 전달하여 화면 렌더링

아래와 같이 간단한 예시를 보겠습니다.
```javascript
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();

  return { props: { data } };
}

function Page({ data }) {
  return <div>{data.message}</div>;
}

export default Page;
```
코드는 SSR과 똑같지만 **SSR**은 **<font color="#1E90FF">서버에서 데이터를 가져와 <code class="highlighter-rouge">HTML</code>을 생성한 뒤 클라이언트에 전송</font>**하는 반면에 **SSG**는 **<font color="#1E90FF">빌드 시 데이터를 미리 가져와 정적인 <code class="highlighter-rouge">HTML</code>파일을 생성</font>**하는 차이점이 있습니다.

![ssg](/assets/img/CSR&SSR&SSG&ISR/ssg.png)

### SSG 장점
---
- 준비된 HTML 파일을 바로 제공하므로 **<font color="#1E90FF">초기 렌더링 속도가 빠름</font>**
- HTML에 콘텐츠가 포함되어 있어 검색 엔진 크롤러가 쉽게 인덱싱이 가능하여 **<font color="#1E90FF">SEO에 최적화됨</font>**

### SSG 단점
---
- HTML은 빌드 시점에 생성되므로, 데이터가 자주 변경되면 다시 빌드가 필요함
- 페이지 수가 많을수록 빌드 시간이 길어짐

## ISR(Incremental Static Regeneration)
---
ISR은 **<font color="#1E90FF">SSG와 SSR의 혼합 방식</font>**으로, 일부 정적 페이지를 특정 간격으로 업데이트합니다. ISR은 `Next.js`에서 제공하는 기능이며, 전체 사이트를 다시 빌드할 필요없이 페이지별로 정적 생성을 할 수 있게 해줍니다.

만약, 브랜드 소개 페이지같이 정적인 페이지도 재빌드가 필요할 때가 있는데 이 경우에 ISR을 사용하면 됩니다.
<blockquote class="prompt-tip">동적인 페이지에 ISR을 사용하면 매번 페이지를 <strong>재빌드</strong> 해야하므로 ISR로는 성능상 문제가 발생하기 때문에 동적인 페이지에는 SSR을 사용하는것이 적합합니다.</blockquote>

### ISR 동작과정
---
- 빌드 시 `HTML`파일을 정적으로 생성
- 브라우저는 정적 `HTML`파일을 제공받음
- 지정된 시간 간격(`revalidate`)으로 서버가 백그라운드에서 `HTML`파일을 갱신

간단한 예시를 보겠습니다.
```javascript
export async function getStaticProps() {
  const res = await fetch('https://api.example.com/data');
  const data = await res.json();

  return {
    props: { data },
    revalidate: 10, // 10초마다 페이지 갱신
  };
}

function Page({ data }) {
  return <div>{data.message}</div>;
}

export default Page;
```
`Next.js`에서 SSG와 동일하지만 특정 시간마다 정적 페이지를 다시 생성합니다.

![isr](/assets/img/CSR&SSR&SSG&ISR/isr.jpg)

### ISR 장점
---
- 기존 SSG처럼 **<font color="#1E90FF">HTML을 정적으로 제공</font>**하므로 초기 로딩이 빠름
- 일정 간격으로 데이터를 갱신하므로 최신 상태 유지
- 준비된 HTML이 제공되므로 **<font color="#1E90FF">크롤러가 쉽게 인덱싱 가능</font>**

### ISR 단점
---
- 데이터가 갱신되기 전까지는 **<font color="#1E90FF">이전 HTML이 제공</font>**될 수 있음
- 갱신 주기를 관리해야 하며, 서버 구성 필요하므로 **<font color="#1E90FF">구현 복잡성이 증가</font>**함

## 요약
---

### CSR
- 대화형 애플리케이션, 사용자 중심 SPA(Single Page Application)
- SEO가 중요하지 않은 경우

### SSR
- 동적인 데이터를 실시간으로 제공
- SEO가 중요한 동적 콘텐츠

### SSG
- 정적 콘텐츠 중심
- SEO와 빠른 로딩 속도가 중요한 경우

### ISR
- SSG의 장점(빠른 로딩)과 SSR의 장점(최신 데이터)을 결합
- 블로그, 뉴스, 자주 업데이트되는 페이지
