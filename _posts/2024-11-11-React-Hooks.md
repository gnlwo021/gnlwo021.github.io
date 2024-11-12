---
title: "[React] 리액트의 핵심 Hook"
date: 2024-11-11
tags: [React]
categories: [React, Basics]
comments: true
---

## 서론
---
리액트의 Hook은 리액트 v16.8부터 새로 도입된 기능으로, 함수형 컴포넌트에서도 상태 관리와 생명주기 기능을 사용할 수 있게 되었습니다. 대표적인 Hook인 **`useState`**, **`useEffect`**같은 상태 관리를 도와주는 기능을 제공하면서 리액트 개발에 유연성과 효율적인 코드 작성을 할 수 있도록 해줍니다. 이번 포스팅에서는 리액트의 다양한 Hook들과 예시를 알아보겠습니다.

## Hook
---
### 📌기본 Hook
---
`useContext`: Context API와 함께 사용하여 컴포넌트 트리 전체에서 **<font color='#1E90FF'>상태를 전역적으로 관리</font>**할 수 있습니다. 주로 전역 상태나 설정이 필요할 때 사용합니다.
```javascript
const value = useContext(MyContext);
```
`useState`: **<font color='#1E90FF'>상태를 정의하고 관리</font>**할 때 사용합니다. 초기 상태 값을 설정할 수 있으며, 상태 값과 상태를 업데이트하는 함수를 반환합니다.
```javascript
const [count, setCount] = useState(0);
const [name, setName] = useState("");
const [arr, setArr] = useState([]);
const [obj, setObj] = useState({});
```
`useEffect`: 컴포넌트가 **<font color='#1E90FF'>렌더링된 이후에 부수 효과를 처리</font>**할 때 사용합니다.
<blockquote class="prompt-tip">부수 효과란? <strong>API 호출, 이벤트 리스너 등록, 타이머 설정</strong>등과 같은 컴포넌트 외부와의 상호작용으로 발생하는 기능을 말합니다.</blockquote>

```javascript
useEffect(() => {
        // 부수 효과 작업 (API 호출, 이벤트 설정 등)
    return () => {
        // 정리 작업 (Cleanup)
    };
}, [dependency]);
```

`useState`와 `useEffect`의 이해를 더 돕기위해 예시를 통해 더 자세히 알아보겠습니다.

### 📌`useState`와 `useEffect`를 사용한 응용 예시
---
#### 최초 렌더링 시 한 번만 실행하기
이 경우에 의존성 배열을 빈 배열(`[]`)로 설정하여 컴포넌트가 **<font color='#1E90FF'>처음 마운트될 때 API 요청이 한 번만 실행</font>**되도록 합니다.

```javascript
import React, { useEffect, useState } from "react";
import axios from "axios";

export default function App() {
  const [data, setData] = useState(null);

  useEffect(() => {
    axios.get("/web/api/count")
      .then((response) => {
        setData(response.data);
      })
      .catch((error) => {
        console.error(error);
      });
  }, []); // 빈 배열로 설정해 최초 마운트 시에만 실행

  return <div>Data: {data ? JSON.stringify(data) : "Loading..."}</div>;
};
```
<blockquote class="prompt-tip"><code class="highlighter-rouge">axios</code>는 리액트 라이브러리로 
<strong>비동기 HTTP(GET, POST, PUT, DELETE)</strong>요청을 할 수 있도록 도와주는 기능을 합니다.</blockquote>

#### 특정 의존성 변경 시마다 실행하기
이 경우는 의존성 배열 특정 상태를 추가하여, 이 값이 변경될 때마다 `useEffect`가 실행되도록 합니다. 즉, 의존성이 변경될 때마다 API 요청을 새로 보낸다는 뜻입니다.
```javascript
...
export default function App({ state }) {
  const [data, setData] = useState(null);

  useEffect(() => {
    axios.get("/web/api/count")
      .then((response) => {
        setData(response.data);
      })
      .catch((error) => {
        console.error(error);
      });
  }, [state]); // state가 변경될 때마다 실행
...
};
```
#### 정리 작업 포함하기
지속적인 연결이 필요한 경우, `useEffect`에서 정리 작업을 수행하여 연결을 안전하게 종료할 수 있습니다.
```javascript
...
export default function App() {
  const [data, setData] = useState(null);

  useEffect(() => {
    const source = axios.CancelToken.source(); // 취소 토큰 생성

    axios.get("/web/api/count", { cancelToken: source.token })
      .then((response) => {
        setData(response.data);
      })
      .catch((error) => {
        if (axios.isCancel(error)) {
          console.log("API 요청이 취소되었습니다.");
        } else {
          console.error(error);
        }
      });

    // 컴포넌트 언마운트 시 취소 작업 수행
    return () => {
      source.cancel();
    };
  }, []); // 최초 마운트 시에만 실행
  ...
};
```
<blockquote class="prompt-tip"><code class="highlighter-rouge">axios.CancelToken</code>는 API 요청을 취소하는 함수입니다.</blockquote>

#### 주기적으로 데이터 갱신하기
타이머를 설정하여 일정 시간마다 API 요청을 보낸 후 데이터를 갱신하고 `useEffect`의 정리 작업으로 타이머를 제거합니다.
```javascript
...
export default function App() {
  const [data, setData] = useState(null);

  useEffect(() => {
    const fetchData = () => {
      axios.get("/web/api/count")
        .then((response) => {
          setData(response.data);
        })
        .catch((error) => {
          console.error(error);
        });
    };

    fetchData(); // 첫 API 호출
    const intervalId = setInterval(fetchData, 5000); // 5초마다 호출

    // 컴포넌트 언마운트 시 타이머 정리
    return () => {
      clearInterval(intervalId);
    };
  }, []); // 빈 배열로 설정해 최초 마운트 시에만 설정
  ...
};
```
#### 상태 의존성으로 조건부 API 호출
의존성배열의 `state`가 특정 값일 때만 API 요청을 보내도록 설정합니다. 여기서는 `state`가 `true`일때만 요청을 보내도록 합니다.
```javascript
...
export default function App({ state }) {
  const [data, setData] = useState(null);

  useEffect(() => {
    if (dependency) { // 조건 확인
      axios.get("/web/api/count")
        .then((response) => {
          setData(response.data);
        })
        .catch((error) => {
          console.error(error);
        });
    }
  }, [state]); // state가 변경될 때마다 실행
  ...
};
```
### 📌성능 최적화 Hook
---
`useMemo`: 값이 변경되지 않으면 **<font color='#1E90FF'>이전 계산 결과를 재사용</font>**하여 불필요한 연산을 방지합니다. 주로 무거운 계산이나 필터링, 정렬 등의 작업에서 사용됩니다.
<blockquote class="prompt-danger"><code class="highlighter-rouge">useMemo</code>는 <strong>값</strong>을 메모이제이션 합니다.</blockquote>

```javascript
const memoizedValue = useMemo(() => {
  console.log('useMemo 연산 시작함');
  const sum = x + y;
  return { sum };
}, [x, y]);

return (
  <div>
    <p>계산 결과: {memoizedValue.sum}</p>
  </div>
);
```
- `x`와 `y`가 변경될 때만 `useMemo`가 다시 계산을 수행합니다. 두 값이 변경되지 않으면 이전에 계산한 결과를 그대로 반환합니다.
- `useMemo`는 계산 함수 `() => { const sum = x + y; return { sum }; }`을 실행하여 결과 값을 반환하고, `memoizedValue`가 그 값을 참조하게 됩니다.

`useCallback`: 메모이제이션된 콜백 함수를 반환하여, 컴포넌트가 리렌더링될 때 **<font color='#1E90FF'>동일한 함수를 유지</font>**하도록 합니다. 주로 자식 컴포넌트에 콜백을 전달할 때 사용합니다.
<blockquote class="prompt-danger"><code class="highlighter-rouge">useCallback</code>은 <strong>함수 자체</strong>를 메모이제이션 합니다.</blockquote>

```javascript
const memoizedFunction = useCallback(() => {
  console.log('useCallback 연산 시작함');
  const sum = x + y;
  return { sum };
}, [x, y]);

// 사용
const result = memoizedFunction();
```
- 의존성 배열 `[x, y]`가 변경될 때만 새로운 함수로 재생성되며, 변경이 없으면 기존 함수를 그대로 사용합니다.
- `memoizedFunction`함수는 `{ sum }`객체를 반환하는 함수를 참조합니다. **`memoizedFunction()`**을 호출해야 `{ sum }`값을 얻을 수 있습니다.

### 📌Ref와 DOM 접근 Hook
---
`useRef`: DOM 요소나 변수 값을 저장할 때 사용합니다. `current` 속성을 통해 **<font color='#1E90FF'>값을 직접 참조</font>**하고, **<font color='#1E90FF'>값이 변경되어도 컴포넌트가 리렌더링되지 않습니다.</font>**
```javascript
const inputRef = useRef(null);

useEffect(() => {
  // 컴포넌트가 마운트될 때 input에 포커스를 설정
  if (inputRef.current) {
    inputRef.current.focus();
  }
}, []); // 빈 배열을 사용해 마운트 시 한번만 실행

return (
  <div>
    <input ref={inputRef} type="text" placeholder="여기에 입력하세요" />
  </div>
);
```
`useImperativeHandle`: `useRef`와 함께 사용하여, 자식 컴포넌트의 메서드를 **<font color='#1E90FF'>부모가 제어할 수 있도록</font>** 합니다.
```javascript
useImperativeHandle(ref, () => ({
  focus: () => {
    inputRef.current.focus();
  }
}));
```
### 📌고급 Hook
`useReducer`: **<font color='#1E90FF'>간단한 상태 관리</font>**가 필요할 때 사용합니다. 상태와 상태를 변경하는 로직을 분리하여 작성할 수 있으며, `Redux`와 유사한 패턴으로 사용할 수 있습니다.
<blockquote class="prompt-tip"> <code class="highlighter-rouge">Redux</code>는 자바스크립트 애플리케이션의 상태를 관리하기 위한 라이브러리로, <code class="highlighter-rouge">Store</code>라는 저장소에 애플리케이션 전체의 상태를 저장하고, 이 상태를 여러 컴포넌트에서 공유하고 관리할 수 있는 기능이 있습니다. 보통 복잡한 상태 관리가 필요할 때는 <code class="highlighter-rouge">Redux</code>, 간단한 상태 관리가 필요할 때는 <code class="highlighter-rouge">useReducer</code>를 사용합니다.</blockquote>

```javascript
const [state, dispatch] = useReducer(reducer, initialState);
```
`useLayoutEffect`: DOM이 변경된 후에 동기적으로 실행됩니다. useEffect와 비슷하지만 **<font color='#1E90FF'>레이아웃이 변경되기 전에 작업을 처리</font>**하고 싶을 때 사용합니다.
```javascript
useLayoutEffect(() => {
  // DOM 조작 등의 작업
}, []);
```
`useDebugValue`: 커스텀 Hook을 **<font color='#1E90FF'>디버깅할 때 사용</font>**할 수 있는 Hook으로, React DevTools에서 값을 표시할 수 있습니다.
```javascript
useDebugValue(isOnline ? "Online" : "Offline");
```
### 📌React Router Hooks
---
`useNavigate`: 페이지 이동을 프로그래밍적으로 제어할 수 있게 도와주는 Hook입니다. 링크 클릭 외에 **<font color='#1E90FF'>특정 이벤트가 발생</font>**했을 때 조건에 따라 페이지를 이동해야 할 때 유용합니다.
```javascript
import { useNavigate } from 'react-router-dom';

export default function App() {
  const navigate = useNavigate();
  
  const handleButtonClick = () => {
    navigate("/home");
  }

  return (
    <button onClick={() => handleButtonClick()}>home</button>
  );
};
```
`useLocation`: 현재 페이지의 **<font color='#1E90FF'>URL 정보를 포함한 위치 객체(location)</font>**를 반환합니다.
```javascript
import { useLocation } from 'react-router-dom';
import styled from "styled-components"
const Style = styled.div`
  .color { 
    color: { props => props.$isHome ? "#000" : "#fff" };
  }
`;
export default function App() {
  const location = useLocation();
  const [isHome, setIsHome] = useState(false);

  useEffect(() => {
    setIsHome(location.pathname === "/home");
  }, []);

  return (
    <div>
      <p $isHome={isHome} className="color">현재 URL 경로: {location.pathname}</p>
    </div>;
  );
};
```
`useParams`: 동적 경로에서 **<font color='#1E90FF'>URL에 포함된 파라미터 값을 추출</font>**합니다. 동적으로 사용자나 게시물 ID를 받는 페이지에서 해당 ID를 쉽게 가져올 수 있습니다.
<blockquote class="prompt-tip">만약, URL이 <code class="highlighter-rouge">/user/123</code>이라면 <code class="highlighter-rouge">userId</code>는 <code class="highlighter-rouge">123</code>이 됩니다.</blockquote>

```javascript
import { useParams } from 'react-router-dom';

export default function App() {
  const { userId } = useParams(); // URL 파라미터에서 userId 추출

  return <h2>User ID: {userId}</h2>;
};
```
`useMatch`: **<font color='#1E90FF'>현재 경로와 특정 경로 패턴의 일치 여부를 확인</font>**하는 데 사용됩니다. 패턴에 맞는 경로와 일치하는지 확인하고, 필요 시 특정 작업을 수행하거나 스타일을 적용할 때 유용합니다.
```javascript
import { useMatch } from 'react-router-dom';

export default function App() {
  const match = useMatch('/home'); // '/home' 경로와 일치 여부 확인

  return (
    <div>
      {match ? 'Home Page' : 'Not on Home Page'}
    </div>
  );
};
```

## 리액트 기초 시리즈
---
[[React] 리액트 유틸리티 메서드](https://gnlwo021.github.io/posts/%EB%A6%AC%EC%95%A1%ED%8A%B8_%EC%9C%A0%ED%8B%B8%EB%A6%AC%ED%8B%B0_%EB%A9%94%EC%84%9C%EB%93%9C/)