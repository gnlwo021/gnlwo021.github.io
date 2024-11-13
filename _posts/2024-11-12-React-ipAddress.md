---
title: "[React] 리액트에서 접속한 IP주소 받아오기"
date: 2024-11-12
tags: [React, ipify API]
categories: [React, Applications]
comments: true
---

## ipify API
--- 
`ipify`는 사용자의 공용 IP 주소를 제공하는 간단한 API 서비스입니다. 웹사이트나 애플리케이션이 **<font color="#1E90FF">인터넷을 통해 접속한 사용자의 IP 주소를 알아내야 할 때</font>**, `ipify API`를 호출하여 공용 IP 주소를 얻을 수 있습니다.

### ipify API 사용방법
---
사용방법은 간단합니다. `https://api64.ipify.org?format=json`를 엔드포인트로 `GET` 요청을 보내면, `JSON` 형식으로 클라이언트의 공용 IP 주소가 응답으로 반환됩니다.
```javascript
useEffect(() => {
  fetch('https://api64.ipify.org?format=json')
    .then(response => response.json())
    .then(data => {
    setIpAddress(data.ip);
    setSearchData({ ...searchData, cnncIpAddr: data.ip });
  })
    .catch(error => console.error('Error fetching IP address:', error));
}, []);
```
위 코드처럼 IP주소가 필요할 때 단순히 `https://api64.ipify.org?format=json`만 요청하면 공용 IP 주소를 응답받을 수 있습니다.

