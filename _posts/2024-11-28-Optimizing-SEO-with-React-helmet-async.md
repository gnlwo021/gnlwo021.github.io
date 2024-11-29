---
title: "[React] CSR에서 발생하는 SEO 문제를 React-helmet-async로 해결하기"
date: 2024-11-28
tags: [React, SEO, CSR]
categories: [React, SEO]
comments: true
---

## CSR의 동작방식과 문제점
---
**CSR**은 웹 애플리케이션에서 콘텐츠의 렌더링을 클라이언트(브라우저)에서 수행하는 방식입니다. CSR의 방식은 사용자 측면에서는 동적인 UI를 제공할 수 있는 장점이 있지만, **SEO(Search Engine Optimization)와 관련해서는 여러 문제를 발생**시킬 수 있는데, SEO측면에서 검색 엔진 크롤러가 JavaScript 실행을 제대로 처리하지 못하거나 콘텐츠 로딩 속도가 느려져 검색 결과에서 누락되는 상황이 발생할 수가 있습니다. SEO측면에서 단점이 많은데도 불구하고 CSR은 리액트의 대표적인 동작 방식으로 널리 사용되고 있습니다.

또한, 리액트는 **SPA**라는 동작방식도 사용하는데, SPA의 역할은 초기 로딩 시 모든 자바스크립트를 로드해야하므로 사용자가 특정 페이지를 접속하지 않더라도 모든 파일을 다운로드 받아야 하기때문에 SSR방식에 비해 상대적으로 느립니다.

이러한 CSR과 SPA의 한계를 조금이나마 극복하고 SEO에 도움이되고자 사용하는 도구가 바로 `React-Helmet-Async`입니다.

## react-helmet-async 설치 및 셋팅
---
`react-helmet-async`패키지를 설치합니다.
```shell
npm install react-helmet-async
```
### react-helmet과 차이점
---

|특징|React-Helmet|React-Helmet-Async|
|------|---|---|
|동시 요청 처리|동시 요청 시 메타 데이터 충돌 가능|동시 요청을 안전하게 처리 가능|
|SSR 지원|복잡한 설정이 필요|SSR 환경에서 간단한 설정으로 사용 가능|
|React Context 사용|사용하지 않음|Context API를 활용하여 데이터 관리|
|프로젝트 규모|소규모 CSR 프로젝트에 적합|SSR 포함 대규모 프로젝트에 적합|
|활성화 여부|유지 보수가 줄어들며 비활성화 상태|활발히 유지 보수 중, 최신 환경에 적합|

표에 나온것 처럼 `React-Helmet`은 기본적인 메타 태그 관리를 지원하며, **소규모 클라이언트 렌더링(CSR) 프로젝트에 적합**하지만, `React-Helmet-Async`는 **SSR과 동시 요청 처리**가 필요한 프로젝트에 더 적합하며, 최신 React 환경에 맞춰져 있어 현재로써 `React-Helmet-Async`를 사용하는 편이 좋습니다.

### HelmetProvider로 감싸기
---
react-helmet-async를 사용하기 위해서 `<HelmetPrivoder>`로 `App.js`를 감싸주어야 합니다.
```javascript
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <BrowserRouter>
      <HelmetProvider>
        <App />
      </HelmetProvider>
    </BrowserRouter>
  </React.StrictMode>
);
```
그 후 `App.js`나 최상위 레이아웃에 `<Helmet>`으로 감싸줍니다.
<!-- <blockquote class="prompt-tip"><code class="highlighter-rouge">map</code>은 리액트에서 많은 비중을 차지하는 메서드입니다. 더 자세한 예시를 통해 알아보겠습니다.</blockquote> -->
<blockquote class="prompt-tip">최상위 레이아웃에 <code class="highlighter-rouge">Helmet</code>을 사용하는 이유는 전체 레이아웃 컴포넌트가 처음 로드될 때 <code class="highlighter-rouge">Helmet</code>이 사용되는 것이 좋기 때문입니다.</blockquote>
```javascript
return (
    <>
        <Helmet>
            <title>Photomag</title>
        </Helmet>
        <GlobalStyle />
        {children}
    </>
)
```

`React-Helmet-Async`를 사용중인 상태에서 `index.html`에 이미 메타태그가 정의되어 있다면 지워줘야 합니다.
하지만 `<meta name="viewport" content="width=device-width, initial-scale=1" />`같이 기본적인 메타태그는 `index.html`에 그대로 두어야 합니다.

### 메타태그를 정의할 컴포넌트 생성
---
```javascript
<Helmet>
  <title>Photomag</title>
  <meta name="description" content="photomag - 雑誌のような写真で素晴らしい瞬間を" />
  <meta property="og:image" content="" />
  <meta property="og:url" content="" />
</Helmet>
```
만약 위와 같이 메타태그를 정의해야 한다면 메타태그를 정의하는 컴포넌트를 생성하여 담아줍니다.
```javascript
const MetaTag = ({
    title = "Photomag",
    description = "雑誌のような写真で素晴らしい瞬間を",
    keywords = "Photomag, magazine, photos"
}) => {
    const location = useLocation();
    const currentUrl = `https://www.photomagstudio.com${location.pathname}`;
    const defaultImg = `${process.env.PUBLIC_URL}/images/photomag_thumbnail.png`;

    return (
        <Helmet>
            <title>{title}</title>

            <meta name="description" content={description} />
            <meta name="keywords" content={`Photomag, magazine, photos, ${keywords}`} />

            <meta property="og:type" content="website" />
            <meta property="og:title" content={title} />
            <meta property="og:site_name" content="Photomag" />
            <meta property="og:description" content={description} />
            <meta property="og:image" content={defaultImg} />
            <meta property="og:url" content={currentUrl} />
            
            <link rel="canonical" href={currentUrl} />

        </Helmet>
    )
};
export default MetaTag;
```
`title`, `description`, `keywords`는 항상 기본값을 갖고있고, 페이지마다 메타태그 컴포넌트를 불러와 페이지에 따라 동적으로 설정합니다.

### 구조화 데이터 추가
구조화 데이터는 검색 엔진이 웹페이지의 내용을 잘 이해하도록 도와주는 **JSON-LD 형식의 메타 데이터**입니다. 우리가 구조화 데이터를 사용하는 이유는 구글에서 페이지의 목적과 구조를 더 잘 이해할 수 있도록 만들고, 
어떤 유형의 컨텐츠를 제공하는지 더 정확하게 인식 할 수 있도록 만들어 줍니다.
```javascript
<script type="application/ld+json">
    {JSON.stringify({
        "@context": "https://schema.org",
        "@type": "WebPage",
        "name": title,
        "description": description,
        "url": currentUrl,
    })}
</script>
```

### 완성된 메타태그 컴포넌트
```javascript
import { Helmet } from 'react-helmet-async';
import { useLocation } from 'react-router-dom';

const MetaTag = ({
    title = "Photomag",
    description = "雑誌のような写真で素晴らしい瞬間を",
    keywords = "Photomag, magazine, photos"
}) => {
    const location = useLocation();
    const currentUrl = `https://www.photomagstudio.com${location.pathname}`;
    const defaultImg = `${process.env.PUBLIC_URL}/images/photomag_thumbnail.png`;

    return (
        <Helmet>
            <title>{title}</title>

            <meta name="description" content={description} />
            <meta name="keywords" content={`Photomag, magazine, photos, ${keywords}`} />

            <meta property="og:type" content="website" />
            <meta property="og:title" content={title} />
            <meta property="og:site_name" content="Photomag" />
            <meta property="og:description" content={description} />
            <meta property="og:image" content={defaultImg} />
            <meta property="og:url" content={currentUrl} />

            <link rel="canonical" href={currentUrl} />

            <script type="application/ld+json">
                {JSON.stringify({
                    "@context": "https://schema.org",
                    "@type": "WebPage",
                    "name": title,
                    "description": description,
                    "url": currentUrl,
                })}
            </script>
        </Helmet>
    )
};
export default MetaTag;
```

### 메타태그 컴포넌트 전역 설정
---
```javascript
import { Route, Routes } from 'react-router-dom';
import SEOMetaTag from "./layout/MetaTag";

function App() {
  return (
    <>
      <SEOMetaTag />
      <Routes>
        <Route path="/" element={<Main />} />
      </Routes>
    </>
  )
}
export default App;
```

## 메타태그 사용
---
```javascript
return (
    <MetaTag 
        title="Photomag"
        description="雑誌のような写真で素晴らしい瞬間を"
        keywords="Photomag home"
    />
...
)
```
다른 컴포넌트에서 설정해둔 메타태그 컴포넌트를 사용하려면 `title`, `description`, `keywords`만 주면 됩니다.

이후 title, 이미지, url을 콘솔로 출력해보면 제대로 작동되는 것을 확인할 수 있습니다.
![메타태그1](/assets/img/Optimizing-SEO-with-React-helmet-async/메타태그1.png)

페이지마다 MetaTag컴포넌트를 사용해서 `title`, `description`, `keywords` 등을 지정해주면 SEO 최적화를 극대화 시킬 수 있습니다.
