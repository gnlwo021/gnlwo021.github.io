---
title: "[React] 라디오 버튼 클릭 시 상단으로 스크롤 되는 문제"
date: 2024-11-08
tags: [React, HTML, CSS]
categories: [React, Issues]
comments: true
---

프로젝트 진행 중, 갑자기 라디오 버튼을 클릭할 때 상단으로 스크롤되는 문제가 발생했습니다.

## 문제 발생
---
```javascript
const RadioWrap = styled.div
    display:flex; 
    gap:10px; 
    margin-bottom:40px; 
    flex-wrap:wrap;
    .radio {
        input {
            position:absolute; 
            left:0; top:0; 
            opacity:0; 
            width:0; 
            height:0; 
            cursor:pointer;
        }
        label {
            display:block; 
            padding:14px 20px; 
            line-height:1; 
            border-radius:8px;
        }
        input:checked + label {
            font-weight:600;
        }
    }
;
```
해당 코드를 적용했을 때, 라디오 버튼을 클릭 시 **<font color='#1E90FF'>스크롤 최상단으로 이동</font>**하는 문제(버그)가 발생했습니다.

## 문제 원인
---
원인을 찾아보니 CSS로 **<font color='#1E90FF'>라디오 버튼을 숨긴 경우</font>**에 주로 발생한다고 합니다.

`position: absolute;`, `opacity: 0;`, `width: 0;`, `height: 0;` 등으로 라디오 버튼을 숨기고 라벨을 클릭 가능한 영역으로 만드는 방법을 사용하고 있을 때, 특정 브라우저나 설정에서 의도하지 않은 동작이 발생할 수 있습니다.

## 문제 해결
---
라디오 버튼 `display`를 `none`으로 변경합니다.
```javascript
input[type="radio"] {
  	display: none;
}
```
### 왜 라디오 버튼을 숨기면 해결될까?
이유는 브라우저의 기본 동작과 클릭 이벤트가 충돌하지 않기 때문입니다. 즉, 아까 보았던 `position: absolute;`, `opacity: 0;`, `width: 0;`, `height: 0;`으로 숨기고 `label`을 클릭 가능한 영역으로 만든 경우, 브라우저는 여전히 라디오 버튼이 페이지에서 존재한다고 간주합니다.

이때, `label`을 클릭하면 브라우저는 숨겨진 라디오 버튼을 활성화시키려고 시도하고, `label`과 라디오 버튼의 위치가 겹치지 않으면 페이지 상단으로 스크롤하는 동작이 발생합니다.

그래서 `display: none;`을 사용해 요소를 DOM에서 완전히 제거하는 것처럼 동작하기 때문에, 브라우저는 해당 요소를 클릭 가능한 영역으로 인식하지 않아서 최상단으로 이동하는 버그가 해결이 됩니다.