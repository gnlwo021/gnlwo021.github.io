---
title: "[React] 자주 사용하는 유틸리티 메서드"
date: 2024-11-11
tags: [React]
categories: [React, Basics]
comments: true
---

## 서론
---
리액트는 프론트엔드 개발에서 가장 널리 사용되는 도구 중 하나로, 효율적인 데이터 처리가 필수입니다. 특히, 효율적인 데이터 처리를 하기 위해서는 다양한 유틸리티 메서드와 Hook 등을 사용하면 데이터를 쉽게 조작하고 관리할 수 있습니다. 데이터를 다루는 데 자주 사용하는 주요 메서드와 활용 예시를 통해 유틸리티 메서드는 어떤 방법으로 사용하는지 알아보겠습니다.

## 유틸리티 메서드
---
### 📌객체(Object) 관련 메서드
---
**`Object.keys(obj)`**: 객체의 모든 **<font color='#1E90FF'>키</font>**를 배열로 반환합니다.
```javascript
const user = { name: "Alice", age: 25, city: "Seoul" };
console.log(Object.keys(user)); // ["name", "age", "city"]
```
`Object.values(obj)`: 객체의 모든 **<font color='#1E90FF'>값</font>**을 배열로 반환합니다.
```javascript
const user = { name: "Alice", age: 25, city: "Seoul" };
console.log(Object.values(user)); // ["Alice", 25, "Seoul"]
```
`Object.entries(obj)`: 객체의 **<font color='#1E90FF'>[key, value] 쌍</font>**을 배열의 배열로 반환합니다.
```javascript
const user = { name: "Alice", age: 25, city: "Seoul" };
console.log(Object.entries(user)); // [["name", "Alice"], ["age", 25], ["city", "Seoul"]]
```
`Object.assign(target, ...sources)`: 여러 객체를 **<font color='#1E90FF'>하나로 병합</font>**할 때 사용합니다.
```javascript
const obj1 = { a: 1 };
const obj2 = { b: 2 };
const result = Object.assign({}, obj1, obj2);
console.log(result); // { a: 1, b: 2 }
```
`Object.fromEntries(entries)`: [key, value] 배열을 **<font color='#1E90FF'>객체로 변환</font>**합니다.
```javascript
const entries = [["name", "Alice"], ["age", 25]];
const obj = Object.fromEntries(entries);
console.log(obj); // { name: "Alice", age: 25 }
```
### 📌배열(Array) 관련 메서드
---
**`map`**: 배열의 각 요소를 변환하여 **<font color='#1E90FF'>새로운 배열</font>**을 생성합니다.
```javascript
const numbers = [1, 2, 3];
const doubled = numbers.map(n => n * 2);
console.log(doubled); // [2, 4, 6]
```
<blockquote class="prompt-tip"><code class="highlighter-rouge">map</code>은 리액트에서 많은 비중을 차지하는 메서드입니다. 더 자세한 예시를 통해 알아보겠습니다.</blockquote>

+ `map` 예시
```javascript
    import React from "react";

    const UsersKeysList = () => {
    const users = [
        { id: 1, name: "Alice", age: 25 },
        { id: 2, name: "Bob", age: 30 },
        { id: 3, name: "Charlie", age: 35 }
    ];

    return (
        <div>
        {users.map((user, index) => (
            <div key={index}>
            <h3>User {index + 1} Keys:</h3>
            <ul>
                {Object.keys(user).map((key) => (
                    <li key={key}>{key}</li>
                ))}
            </ul>
            </div>
        ))}
        </div>
    );
};
export default UsersKeysList;
```
`users` 배열에 있는 객체의 키를 `Object.keys`로 추출하고 각 키를 화면에 표시하도록 하는 코드입니다.

`filter`: **<font color='#1E90FF'>조건에 맞는 요소만 추출</font>**하여 새로운 배열을 생성합니다.
```javascript
const numbers = [1, 2, 3, 4];
const evenNumbers = numbers.filter(n => n % 2 === 0);
console.log(evenNumbers); // [2, 4]
```
`reduce`: 배열을 순회하며 **<font color='#1E90FF'>누적 결과값</font>**을 반환합니다.
```javascript
const numbers = [1, 2, 3, 4];
const sum = numbers.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // 10
```
`forEach`: 배열의 각 요소에 대해 작업을 수행합니다.
```javascript
const fruits = ["apple", "banana", "cherry"];
fruits.forEach(fruit => console.log(fruit)); // apple, banana, cherry
```
`find`: **<font color='#1E90FF'>조건에 맞는 첫 번째 요소</font>**를 반환합니다.
```javascript
const users = [{ id: 1, name: "Alice" }, { id: 2, name: "Bob" }];
const user = users.find(user => user.id === 2);
console.log(user); // { id: 2, name: "Bob" }
```
`some`: 배열 내 **<font color='#1E90FF'>하나 이상의 요소가 조건을 만족</font>**하는지 확인합니다.
```javascript
const numbers = [1, 2, 3];
const hasEven = numbers.some(n => n % 2 === 0);
console.log(hasEven); // true
```
`every`: **<font color='#1E90FF'>모든 요소가 조건을 만족</font>**하는지 확인합니다.
```javascript
const numbers = [2, 4, 6];
const allEven = numbers.every(n => n % 2 === 0);
console.log(allEven); // true
```
`includes`: 배열에 **<font color='#1E90FF'>특정 값이 포함</font>**되어 있는지 확인합니다.
```javascript
const fruits = ["apple", "banana"];
console.log(fruits.includes("apple")); // true
```
`concat`: **<font color='#1E90FF'>배열을 합쳐 새로운 배열</font>**을 생성합니다.
```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];
const combined = arr1.concat(arr2);
console.log(combined); // [1, 2, 3, 4]
```
`slice`: **<font color='#1E90FF'>배열의 일부를 새로운 배열</font>**로 반환합니다.
```javascript
const fruits = ["apple", "banana", "cherry"];
const sliced = fruits.slice(1, 3);
console.log(sliced); // ["banana", "cherry"]
```
`splice`: 배열에서 **<font color='#1E90FF'>요소를 추가, 제거하거나 교체</font>**할 때 사용합니다.
```javascript
const fruits = ["apple", "banana", "cherry"];
fruits.splice(1, 1, "blueberry");
console.log(fruits); // ["apple", "blueberry", "cherry"]
```
### 📌문자열(String) 관련 메서드
---
`trim`: 문자열 **<font color='#1E90FF'>양끝의 공백을 제거</font>**합니다.
```javascript
const str = "  hello world  ";
console.log(str.trim()); // "hello world"
```
`toLowerCase` / `toUpperCase`: 문자열을 **<font color='#1E90FF'>소문자 혹은 대문자</font>**로 변환합니다.
```javascript
const str = "Hello World";
console.log(str.toLowerCase()); // "hello world"
```
`split`: 특정 구분자를 기준으로 **<font color='#1E90FF'>문자열을 나누어</font>** 배열로 반환합니다.
```javascript
const str = "apple,banana,cherry";
const fruits = str.split(",");
console.log(fruits); // ["apple", "banana", "cherry"]
```
`replace`: 특정 문자열을 **<font color='#1E90FF'>다른 문자열로 대체</font>**합니다.
```javascript
const str = "Hello World";
const newStr = str.replace("World", "React");
console.log(newStr); // "Hello React"
```
`includes`: 문자열에 **<font color='#1E90FF'>특정 문자열이 포함</font>**되어 있는지 확인합니다.
```javascript
const str = "Hello World";
console.log(str.includes("World")); // true
```
### 📌기타 유용한 메서드
---
`JSON.stringify`: 객체를 **<font color='#1E90FF'>JSON 형식의 문자열</font>**로 변환합니다.
```javascript
const obj = { name: "Alice", age: 25 };
const json = JSON.stringify(obj);
console.log(json); // '{"name":"Alice","age":25}'
```
`JSON.parse`: JSON 형식의 문자열을 **<font color='#1E90FF'>객체</font>**로 변환합니다.
```javascript
const json = '{"name":"Alice","age":25}';
const obj = JSON.parse(json);
console.log(obj); // { name: "Alice", age: 25 }
```
`Array.isArray`: 주어진 값이 **<font color='#1E90FF'>배열인지 확인</font>**합니다.
```javascript
const arr = [1, 2, 3];
console.log(Array.isArray(arr)); // true
```
`Number.isNaN`: 값이 **<font color='#1E90FF'>NaN인지 확인</font>**합니다.
```javascript
console.log(Number.isNaN(NaN)); // true
```
`parseInt` / `parseFloat`: 문자열을 **<font color='#1E90FF'>정수 또는 실수로 변환</font>**합니다.
```javascript
const int = parseInt("42");
const float = parseFloat("3.14");
console.log(int); // 42
console.log(float); // 3.14
```

## 리액트 기초 시리즈
---
[[React] 리액트의 핵심 Hook](https://gnlwo021.github.io/posts/%EB%A6%AC%EC%95%A1%ED%8A%B8_Hooks/)