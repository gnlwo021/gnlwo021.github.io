---
title: "[React] 파일 다운로드 시 새탭이 켜지는 문제"
date: 2024-11-08
tags: [React, AWS]
categories: [React, Issues]
comments: true
---

AWS S3와 React를 사용하면서 파일 다운로드 기능을 구현하던 중, 다운로드 버튼을 클릭했을 때 **<font color='#1E90FF'>파일이 다운로드되지 않고 새 탭이 열리는 문제</font>**가 발생했습니다.

## 문제 발생
---
```javascript
const fileDownloadClick = async (fileName, dirName) => {
  const params = { fileName: fileName, dirName: dirName };

  const response = await fetch(`/web/api/svc-mgt/iqry/download?fileName=${fileName}&dirName=${dirName}`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(params),
  });

  if (!response.ok) {
    throw new Error('다운로드 요청 실패');
  }

  const fileUrl = await response.text();
  const link = document.createElement('a');
  link.href = fileUrl;
  link.download = fileName;
  link.click();
};
```
위 코드를 보시다시피 `fileName`, `dirName`을 파라미터로 받아 `<a>`링크를 만든 후 `link`가 파일을 가르키도록 만들고 `download` 속성에 `fileName`을 설정하여 파일이 지정한 이름으로 다운로드되도록 하며, `link.click()`을 실행하여 다운로드 링크를 자동으로 클릭하도록 만든 일반적인 파일 다운로드 코드입니다.

하지만, 위 코드에서 `fileDownloadClick()`를 호출해 파일 다운로드를 하려했지만 파일 다운로드가 되지 않고 새탭이 켜지는 문제가 발생했습니다.

링크 이동(새탭)이 아닌 파일을 직접 다운로드 하기 위해서는 `<a>`요소의 다운로드 속성을 사용하는데, `window.open()`과 같이 다운로드가 아닌 새 탭에서 파일을 보여주는 방식으로 동작했습니다.

## 문제 원인
---
파일 다운로드가 되지 않고 새 탭이 열리는 원인을 찾아본 결과, 주로 다음과 같은 3가지 문제가 대표적이라는 것을 알게 되었습니다.
1. **다운로드를 위한 링크가 Blob 또는 파일 URL에 적절한 헤더가 포함되어야 함.**
> 이미 콘솔에서 res를 출력한 결과로 url을 성공적으로 반환이 된 것을 확인했기 때문에 문제x

2. **파일이 올바르게 인식되지 않음.**
> 실제 url을 브라우저에서 접속한 결과 이미지가 새탭으로라도 성공적으로 표시됐기 때문에 문제x

3. **서버에서 반환되는 `Content-Disposition`헤더가 다운로드로 설정되어 있지 않음.**
> 파일을 다운로드 할 때 `Content-Disposition`헤더가 설정되어있는지 확인x

> `Content-Disposition` 헤더는 HTTP 응답 헤더 중 하나로, 서버가 브라우저에게 파일을 어떻게 처리해야 하는지 알려주는 역할을 합니다. 이 헤더는 주로 파일 다운로드 기능을 구현할 때 사용되며, 브라우저가 파일을 직접 열지 않고 다운로드하도록 유도할 수 있습니다.

## 문제 해결
---
우선, AWS S3 콘솔에서 문제가 있는 버킷으로 이동 → 권한 탭 선택 → 맨 밑으로 이동하면 CORS가 있는것을 확인 후 편집 클릭 → CORS규칙에서 `Content-Disposition`헤더를 추가했습니다.
```json
[
    {
        "AllowedHeaders": [
            "*"
        ],
        "AllowedMethods": [
            "GET",
            "POST",
            "PUT",
            "HEAD"
        ],
        "AllowedOrigins": [
            "*"
        ],
        "ExposeHeaders": [
            "Content-Disposition",
        ],
        "MaxAgeSeconds": 3000
    }
]
```
CORS를 설정 후 파일 다운로드 코드의 res를 `Blob`으로 변환해 다운로드를 트리거하는 방법으로 수정합니다.
```javascript
const fileDownloadClick = async (fileName, dirName) => {
  const params = { fileName: fileName, dirName: dirName };

  const response = await fetch(`/web/api/svc-mgt/iqry/download?fileName=${fileName}&dirName=${dirName}`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify(params),
  });

  if (!response.ok) {
    throw new Error('다운로드 요청 실패');
  }

  // 응답을 Blob으로 변환
  const blob = await response.blob();

  // Blob을 가리키는 URL 생성
  const fileUrl = URL.createObjectURL(blob);

  // 다운로드 링크 생성 및 트리거
  const link = document.createElement('a');
  link.href = fileUrl;
  link.download = fileName;
  document.body.appendChild(link); // 링크를 DOM에 추가
  link.click(); // 다운로드 트리거
  document.body.removeChild(link); // 링크 삭제

  // URL 해제하여 메모리 절약
  URL.revokeObjectURL(fileUrl);
};
```

`Blob`형태로 변환하여 트리거하면 해결이 되지만 `Access-Control-Allow-Origin` 에러가 났습니다.
```shell
Reason: CORS header 'Access-Control-Allow-Origin' missing
```
위 에러는 CORS정책에서 파일 URL에 대해 적절한 헤더를 설정하지 않았다는 뜻인데, `POST` → `GET`요청으로 변경하여 재실행 하였더니 문제가 해결되었습니다!

