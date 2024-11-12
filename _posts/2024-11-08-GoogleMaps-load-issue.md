---
title: "[React] 화면을 전환할 때 Google Maps가 로드되지 않는 문제"
date: 2024-11-08
tags: [React, Google Maps API]
categories: [React, Issues]
comments: true
---

프로젝트 진행 중, **지도가 있는 페이지에서 뒤로가기 했다가 다시 지도가 로드되는 페이지로 이동하면 Google Maps가 로드되지 않는 문제**가 발생했습니다.

## 문제 발생
---
![google Maps error1](/assets/img/google_maps_error1.png)
```javascript
  return (
    <LoadScript googleMapsApiKey="YOUR_API_KEY"> {/* API 키 입력 */}
      <GoogleMap
        mapContainerStyle={containerStyle}
        center={defaultCenter}
        zoom={15}  // 적절한 줌 레벨 설정
        options={uiOption}
      >
        <MarkerF position={defaultCenter} />
      </GoogleMap>
    </LoadScript>
  );
```
React에서 Google Maps API를 사용해 지도를 로드했지만, 뒤로가기 후 다시 지도가 있는 페이지로 이동할 때 Google Maps가 로드되지 않고 `...Loading`이라는 문구만 표시되는 현상이 발생했고, 콘솔에 다음과 같은 경고 메시지가 출력됐습니다.
```shell
Google Maps JavaScript API has been loaded directly without loading=async. 
This can result in suboptimal performance. For best-practice loading patterns 	  
please see https://goo.gle/js-api-loading
```
해당 에러는 **Google Maps API가 최적화 되지 않은 방식**으로 로드되었으며, Google Maps를 **비동기(async)**로 로드하지 않아 발생한 것이라고 합니다.

`<LoadScript>`컴포넌트는 Google Maps API의 컴포넌트로, Google Maps를 비동기로 로드해주는 역할을 합니다. 그런데 비동기로 로드되지 않는다고 경고 메시지가 뜨니 이해가 되지 않았습니다.🤔

## 문제 원인
---
문제가 될 만한 `<LoadScript>`에 대해 찾아보니 **Google Maps API가 이미 로드된 경우에는 다시 로드하지 않지만**, 컴포넌트의 구조에 따라 **<font color='#1E90FF'>페이지 이동</font>**이나 **<font color='#1E90FF'>상위 컴포넌트의 상태 변화</font>**로 인해 `<LoadScript>`가 리렌더링되면 렌더링 중복 문제가 발생할 수 있다는 것입니다. 

즉, 페이지 이동 후 다시 돌아왔을 때 `<LoadScript>`가 리렌더링되면, Google Maps 스크립트와 충돌이 발생하여 상태가 초기화되면서 지도 로드가 중단된다는 뜻입니다.

## 문제 해결
---
지도를 로드할 때 로드 문제가 발생하니 `<LoadScript>`를 제거하고 Google Maps API Script를 비동기로 로드해주는 역할을 하는 `useLoadScript()`함수를 사용했습니다.

### useLoadScript() 사용
```javascript
    const { isLoaded, loadError } = useLoadScript({
        googleMapsApiKey: 'user google api key',
        libraries: libraries,
    });

    if (loadError) return <div>Error loading maps</div>;
    if (!isLoaded) return <div>Loading Maps...</div>; 

...
    return (
        <GoogleMap
            mapContainerStyle={containerStyle}
            center={defaultCenter}
            zoom={15}  // 적절한 줌 레벨 설정
            options={uiOption}
        >
            <MarkerF position={defaultCenter} />  {/* 마커 설정 */}
        </GoogleMap>
    );
```
<blockquote class="prompt-tip"><code class="highlighter-rouge">useLoadScript()</code>훅을 사용할 때는 <strong>Google Maps API가 제대로 로드될 때 까지 기다린 후 처리</strong>를 해야하기 때문에 조건문을 사용해 API가 로드된 후 로직이 실행되도록 설정했습니다.</blockquote>

## 결과
![google Maps error2](/assets/img/google_maps_error2.png)

`useLoadScript()`함수를 사용하니 Google Maps가 정상적으로 로드가 되었습니다!