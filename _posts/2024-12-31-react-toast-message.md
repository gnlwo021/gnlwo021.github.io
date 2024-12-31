---
title: "[React] react-Toastify로 토스트 메시지 띄워보기"
date: 2024-12-31
tags: [Raect, react-toastify, Message]
categories: [React, Applications]
comments: true
--- 

![react-toast1](../assets/img/react-toast/react_toast1.png)

위 이미지처럼 `React-Toastify`라이브러리로 클립보드 복사 메시지를 띄워보려고 합니다.

`React-Toastify`는 비교적 간단한 코드로 구현이 가능합니다.

## React-Toastify 설정
---

### 설치
---

```bash
npm install react-toastify
```

### ToastProvider 컴포넌트 생성
---

```javascript
import React from 'react';
import { toast, ToastContainer } from 'react-toastify';
import 'react-toastify/dist/ReactToastify.css';

export const notify = (message, type = 'success') => {
  const options = {
    position: "bottom-right", // 메시지 위치
    autoClose: 2000, // 2초 후 자동 닫힘
    hideProgressBar: false, // 진행 상태 표시 바를 숨길지 여부
    closeOnClick: true,
    pauseOnHover: true,
    draggable: true,
    theme: "dark", // 다크 테마
  };

  if (type === 'success') {
    toast.success(message, options);
  } else if (type === 'error') {
    toast.error(message, options);
  }
};

export const ToastProvider = () => {
  return <ToastContainer />;
};
```

### App.jsx
---

```javascript
import React, { useEffect } from 'react';
import { notify, ToastProvider } from './CustomToast';

export default function App({ sanitizedHTML }) {
  useEffect(() => {
    const hashtags = document.querySelectorAll('.hashtag');
    hashtags.forEach((span) => {
      span.addEventListener('click', () => {
        const text = span.innerText.replace(/^#\s*/, '');
        navigator.clipboard
          .writeText(text)
          .then(() => {
            notify(`"${text}" 복사 성공!`);
          })
          .catch((err) => {
            notify('복사 실패: 클립보드 접근 불가', 'error');
            console.error('복사 실패 : ', err);
          });
      });
    });

    return () => {
      hashtags.forEach((span) => {
        span.removeEventListener('click', () => {});
      });
    };
  }, [sanitizedHTML]);

  return (
    <div>
      <div dangerouslySetInnerHTML={{ __html: sanitizedHTML }} />
      {/* ToastContainer를 페이지에 포함 */}
      <ToastProvider />
    </div>
  );
}
```

`hashtag`클래스가 있는 `span`요소를 클릭 시 `navigator.clipboard`를 이용하여 클립보드에 복사 하고 `ToastProvider`컴포넌트를 불러와 사용하면 완성입니다!