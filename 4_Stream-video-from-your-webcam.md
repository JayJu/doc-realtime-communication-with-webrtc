# 4. 웹캠으로 동영상 스트리밍하기

## 여기서 배우게 될 것

* 웹캠을 사용해 동영상 스트림 얻기
* 스트림 전송 다루기
* 동영상을 다루기 위한 CSS 와 SVG 사용하기

이번 단계의 전체 소스는 **step-01** 폴더에 있다.

## HTML 맛보기

**작업디렉토리** 내 **index.html**에 `video` 와 `script`엘리먼트를 추가한다.

``` html
<!DOCTYPE html>
<html>

<head>

 <title>Realtime communication with WebRTC</title>

 <link rel="stylesheet" href="css/main.css" />

</head>

<body>

 <h1>Realtime communication with WebRTC</h1>

 <video autoplay></video>

 <script src="js/main.js"></script>

</body>

</html>
```

## JavaScript 삽입하기
**js**디렉토리에 **main.js** 파일을 추가한다.

``` javascript
'use strict';

navigator.getUserMedia = navigator.getUserMedia || navigator.webkitGetUserMedia || navigator.mozGetUserMedia;

var constraints = { audio: false, video: true};var video = document.querySelector('video');

function successCallback(stream) { window.stream = stream; // stream available to console if (window.URL) { video.src = window.URL.createObjectURL(stream); } else { video.src = stream; }}

function errorCallback(error) { console.log('navigator.getUserMedia error: ', error);}

navigator.getUserMedia(constraints, successCallback, errorCallback);

```
> 예제에서 사용하는 모든 Javascript는 `common coding gotchas`를 피하기 위해 `use strict'` 를 사용한다. 상세설명은 [ECMAScript 5 Strict Mode, JSON, and More](http://ejohn.org/blog/ecmascript-5-strict-mode-json-and-more/)를 참고.

## 테스트하기
브라우저에서 **index.html**를 열어 아래와같은 웹캠영상이 뜨는지 확인한다.
![](/img/ch401.png)