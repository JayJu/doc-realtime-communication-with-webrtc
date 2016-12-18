# 9. 사진을 찍고 데이터 채널을 통해 공유해 보자

## 여기서 배우게 될 것

* canvas 요소를 사용하여 사진을 찍은 다음 그 데이터를 가져오는 방법
* 원격 사용자와 이미지 데이터를 교환하는 방법

전체 소스는 **step-06** 디렉토리에 있다.

## 어떻게 동작하나
이전에는 RTCDataChannel을 사용하여 텍스트 메시지를 교환하는 방법을 배웠다.

이번 장에서는 전체 파일을 공유 해 보자: 이 예제에서는 ```getUserMedia()```를 통해 캡처 한 사진들이다.

이 단계의 핵심 부분은 다음과 같다.

1. 데이터 채널을 설정한다. 이 단계에서는 피어 연결에 미디어 스트림을 추가하지 않는다.

2. ```getUserMedia()```를 사용하여 사용자의 웹캠 비디오 스트림을 캡처한다 :

  ``` javascript
  var video = document.getElementById('video');

  function grabWebCamVideo() {
    console.log('Getting user media (video) ...');
    navigator.mediaDevices.getUserMedia({
      audio: false,
      video: true
    })
    .then(gotStream)
    .catch(function(e) {
      alert('getUserMedia() error: ' + e.name);
    });
  }
  ```
3. 사용자가 스냅 버튼을 클릭하면 비디오 스트림에서 스냅샷(비디오 프레임)을 가져와 캔버스 요소에 표시한다.

  ``` javascript
  var photo = document.getElementById('photo');
  var photoContext = photo.getContext('2d');

  function snapPhoto() {
    photoContext.drawImage(video, 0, 0, photo.width, photo.height);
    show(photo, sendBtn);
  }
  ```

4. 사용자가 보내기 버튼을 클릭하면 이미지를 바이트로 변환하고 데이터 채널을 통해 전송한다.

  ``` javascript
  function sendPhoto() {
    // Split data channel message in chunks of this byte length.
    var CHUNK_LEN = 64000;
    var img = photoContext.getImageData(0, 0, photoContextW, photoContextH),
      len = img.data.byteLength,
      n = len / CHUNK_LEN | 0;

    console.log('Sending a total of ' + len + ' byte(s)');
    dataChannel.send(len);

    // split the photo and send in chunks of about 64KB
    for (var i = 0; i < n; i++) {
      var start = i * CHUNK_LEN,
        end = (i + 1) * CHUNK_LEN;
      console.log(start + ' - ' + (end - 1));
      dataChannel.send(img.data.subarray(start, end));
    }

    // send the reminder, if any
    if (len % CHUNK_LEN) {
      console.log('last ' + len % CHUNK_LEN + ' byte(s)');
      dataChannel.send(img.data.subarray(n * CHUNK_LEN));
    }
  }
  ```

5. 수신 측은 데이터 채널 메시지 바이트를 이미지로 다시 변환하고 이미지를 사용자에게 표시한다.

  ``` javascript
  function receiveDataChromeFactory() {
    var buf, count;

    return function onmessage(event) {
      if (typeof event.data === 'string') {
        buf = window.buf = new Uint8ClampedArray(parseInt(event.data));
        count = 0;
        console.log('Expecting a total of ' + buf.byteLength + ' bytes');
        return;
      }

      var data = new Uint8ClampedArray(event.data);
      buf.set(data, count);

      count += data.byteLength;
      console.log('count: ' + count);

      if (count === buf.byteLength) {
        // we're done: all data chunks have been received
        console.log('Done. Rendering photo.');
        renderPhoto(buf);
      }
    };
  }

  function renderPhoto(data) {
    var canvas = document.createElement('canvas');
    canvas.width = photoContextW;
    canvas.height = photoContextH;
    canvas.classList.add('incomingPhoto');
    // trail is the element holding the incoming images
    trail.insertBefore(canvas, trail.firstChild);

    var context = canvas.getContext('2d');
    var img = context.createImageData(photoContextW, photoContextH);
    img.data.set(data);
    context.putImageData(img, 0, 0);
  }
  ```

## 코드 가져오기
**작업** 디렉토리의 내용을 **step-06** 의 내용으로 바꾼다. **작업** 중인 **index.html** 파일은 다음과 같다.

``` javascript
<!DOCTYPE html>
<html>

<head>

  <title>Realtime communication with WebRTC</title>

  <link rel="stylesheet" href="/css/main.css" />

</head>

<body>

  <h1>Realtime communication with WebRTC</h1>

  <h2>
    <span>Room URL: </span><span id="url">...</span>
  </h2>

  <div id="videoCanvas">
    <video id="camera" autoplay></video>
    <canvas id="photo"></canvas>
  </div>

  <div id="buttons">
    <button id="snap">Snap</button><span> then </span><button id="send">Send</button>
    <span> or </span>
    <button id="snapAndSend">Snap &amp; Send</button>
  </div>

  <div id="incoming">
    <h2>Incoming photos</h2>
    <div id="trail"></div>
  </div>

  <script src="/socket.io/socket.io.js"></script>
  <script src="js/lib/adapter.js"></script>
  <script src="js/main.js"></script>

</body>

</html>
```

노드 서버가 실행되고 있지 않으면 작업 디렉토리에서 다음 명령을 실행한다.

``` shellscript
node index.js
```

(Socket.IO를 구현한 **index.js** 버전을 사용하고 있는지 확인하고 변경 한 경우 노드 서버를 다시 시작한다)

필요한 경우 **허용(Allow)** 버튼을 클릭하면 앱이 웹캠을 사용할 수 있다.

이 어플리케이션은 임의로 회의실 ID를 만들고 해당 ID를 URL에 추가한다. 새 브라우저 탭 또는 창에서 이 URL에 접속한다.

** 스냅 및 보내기(Snap & Send)** 버튼을 클릭 한 다음 페이지 하단의 다른 탭에서 수신 영역을 확인한다. 앱이 탭간에 사진을 전송한다.

다음과 같은 내용을 확인한다:
![](/img/ch901.jpg)

## 보너스 점수
1. 파일 형식을 공유하려면 코드를 어떻게 변경해야 할까?

## 좀 더 찾아보기
* [MediaStream Image Capture API](https://www.chromestatus.com/features/4843864737185792): 사진을 찍고 카메라를 제어하는 API - 가까운 브라우저에서 곧 사용할 수 있다!
* 오디오 및 비디오 녹음을위한 MediaRecorder API: [데모](https://webrtc.github.io/samples/src/content/getusermedia/record/), [설명서](https://www.chromestatus.com/features/5929649028726784).

## 지금까지 배운 것들

이번 장에서는

* canvas 요소를 사용하여 사진을 찍은 다음 그 데이터를 가져오는 방법
* 원격 사용자와 이미지 데이터를 교환하는 방법

에 대해 배웠다. 전체 소스는 **step-06** 디렉토리에 있다.

