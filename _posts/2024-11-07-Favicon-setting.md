---
title: GitHub Blog - 파비콘(favicon) 세팅하기
date: 2024-11-12
tags: [Blogging, Git, Jekyll, favicon]
categories: [GitHub Blog]
comments: true
---

<blockquote class="prompt-warning"><code class="highlighter-rouge">/_includes/_head/</code>경로에 <code class="highlighter-rouge">custom.html</code>파일이 없었기 때문에 favicon폴더에 있는 기본 이미지를 새로운 이미지로 덮어씌우는 방식으로 적용했습니다.</blockquote>

## 파비콘(favicon)
---
웹사이트의 아이콘으로, 웹 상단 주소창에 작은 아이콘으로 표시되는 대표적인 아이콘입니다.

`jekyll`을 사용중일 때, 기본 이미지는 다음과 같이 나타납니다.

![favicon1](/assets/img/favicon-setting-img/favicon1.png)

자신의 블로그를 만들기 위해서는 기본 이미지가 아닌 자신만의 이미지를 파비콘으로 설정해야 합니다. 지금부터 파비콘을 설정하는 방법을 알아보겠습니다.

### 원하는 이미지 찾기
---
우선, 이미지 파일이 필요하실테니 이미지를 직접 만들어도 되고, 파비콘 이미지를 다운로드 받는 사이트에서 원하는 이미지(이모지)를 다운로드 받아 사용해도 됩니다.

저는 원하는 이미지(이모지)를 [favicon.io](https://favicon.io/emoji-favicons/)에서 다운로드 받았습니다.

<blockquote class="prompt-tip"><code class="highlighter-rouge">Download</code>를 클릭하여 Zip파일을 다운로드 받습니다.</blockquote>

![favicon2](/assets/img/favicon-setting-img/favicon2.png)

### 파비콘을 favicon 폴더에 붙여넣기
---
이미지를 다운로드 받은 후 Zip폴더 압축을 푼 뒤, `about.txt`를 제외한 모든 파일을 `/assets/img/favicons`경로에 있는 기본 이미지에 덮어씌워줍니다.

![favicon3](/assets/img/favicon-setting-img/favicon3.png)

아래 이미지처럼 붙여넣었습니다.

![favicon4](/assets/img/favicon-setting-img/favicon4.png)

### 파비콘 적용 확인
---
로컬 서버를 재가동 합니다.
```shell
jekyll serve
```
캐시 비우기 및 강력 새로고침을 하면 파비콘이 적용된 모습을 볼 수 있습니다!

![favicon5](/assets/img/favicon-setting-img/favicon5.png)