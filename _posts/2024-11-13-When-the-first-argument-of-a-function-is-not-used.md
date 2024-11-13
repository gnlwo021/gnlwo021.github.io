---
title: "[React] 함수에 첫 번째 인자(parameter)를 사용하지 않는 경우"
date: 2024-11-13
tags: [React, event]
categories: [React, Applications]
comments: true
---

## 첫 번째 인자를 사용하는 경우
---
첫 번째 인자는 보통 **<font color="#1E90FF">이벤트 객체로 자동 전달</font>**되지만, 우리가 실제로 사용하고자 하는 데이터는 **두 번째 인자로 전달되는 경우가 많습니다.**
```javascript
const handleButtonClick = (event, data) => {
    event.preventDefault();
    console.log(data);
};

<button onClick={(e) => handleButtonClick(e, "클릭된 데이터")}>Click Me</button>
```
그런데, 아래 코드처럼 `event`객체가 필요하지 않는 경우가 있습니다.
```javascript
const handleButtonClick = (event, data) => {
    setData(data);
};
```
이런 경우에 `event`객체를 사용하지 않기 때문에 그냥 두자니 불필요한 코드가 되버려서 코드가 깔끔해지지 않고, `event`객체를 제거해버리면 뒤에 있는 `data`가 첫 번재 인자가 되어버려서 원하지 않는 코드가 됩니다.

이럴 때 필요한게 `_`입니다. javascript에서는 사용하지 않는 인자 자리에 `_`를 넣으면 불필요하다는 표시로 사용할 수 있습니다.

## 첫 번재 인자를 사용하지 않는 경우
---
첫 번째 인자가 필요하지 않을 때는 `_`로 표시하고 무시합니다.
```javascript
const handleButtonClick = (_, data) => {
    setData(data);
};
```

## 요약
---
- **첫 번째 인자를 사용하는 경우**: 이벤트 핸들러에서 `event` 객체를 사용하여 기본 동작을 막거나 특정 정보를 가져와야 할 때 사용합니다.
- **첫 번째 인자를 사용하지 않는 경우**: 이벤트 객체가 필요 없고, 두 번째 인자만 사용할 때 `_`로 첫 번째 인자를 생략하여 불필요한 코드를 방지합니다.

