# 4. 웹캠으로 동영상 스트리밍하기

## 여기서 배우게 될 것

* 웹캠을 사용해 동영상 스트림 얻기
* 스트림 전송 다루기
* 동영상을 다루기 위한 CSS 와 SVG 사용하기

이번 단계의 전체 소스는 **step-01** 폴더에 있다.

## HTML 맛보기

**작업디렉토리** 내 **index.html**에 `video` 와 `script`엘리먼트를 추가한다.

```html
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

```javascript
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

> gUM API의 더 나은 사용방법
> 만약 위 코드가 옛날 스타일이라 생각했다면 맞다.
> 우리는 현재 브라우저들의 호환성을 위해 `getUserMedia()`의 콜백 버전을 사용했다.
> [MediaDevices API](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices)와 더 나은 에러핸들링을 이용한 Promise 버전은 [https:\/\/github.com\/webrtc\/samples](https://github.com/webrtc/samples) 데모에서 확인할 수 있다.

## 어떻게 동작할까?

`getUserMedia()`는 아래와 같이 호출된다.

```javascript
navigator.getUserMedia(constraints, successCallback, errorCallback);
```

이 방법이 상대적으로 신기술인 이유로 브라우져들이 아직까진 `getUserMedia`를 접두어로 네이밍하고 있다. **main.js** 의 가장 상단에 이 코드를 넣은것도 그런 이유 때문이다.

`constraints` 인자는 가져올 미디어를 지정하는데 사용한다. - 여기서는 음성은 제외하고 동영상만 지정한다.

```javascript
var constraints = {
  audio: false,
  video: true
};
```

`getUserMedia()`가 성공하면 웹캠의 동영상 스트림이 video 요소의 소스로 설정된다.

```javascript
function successCallback(stream) {
  window.stream = stream; // stream available to console
  if (window.URL) {
    video.src = window.URL.createObjectURL(stream);
  } else {
    video.src = stream;
  }
}
```

## 보너스 팁

* `getUserMedia()`로 전달된 `stream`오브젝트는 전역 스코프 이므로 브라우저 콘솔을 열어 _stream_ 입력 후 엔터로 검사해볼 수 있다.\(맥 사용자의 크롬 콘솔을 보는 방법은 Ctrl-Shilt-J 또는 Command-Option-J 이다\)
* `stream.getVideoTracks()` 함수의 리턴값은 무엇인가?
* `stream.getVideoTracks()[0].stop()`을 호출해 보자.
* constraints 오브젝트를 확인해 보자: `{audio:true, video:true}` 로 변경하면 어떤 결과가 나올까?
* 동영상의 크기는 얼마나 되는가? 화면에 출력되는 사이즈가 아닌 실제 사이즈를 javascript로 가져오려면 어떻게 해야 하나? 크롬 개발자도구를 확인해보자.
* 아래와 같이 video 요소에 CSS 필터를 추가해 보자.
  ```javascript
  video {
  -webkit-filter: blur(4px) invert(1) opacity(0.5);
  }
  ```

* 아래와 같이 SVG 필터를 추가해 보자.
  ```javascript
  video {
   filter: hue-rotate(180deg) saturate(200%);
   -moz-filter: hue-rotate(180deg) saturate(200%);
   -webkit-filter: hue-rotate(180deg) saturate(200%);
  }
  ```


