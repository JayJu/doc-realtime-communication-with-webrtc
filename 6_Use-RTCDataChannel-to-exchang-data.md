# 6. RTCDataChannel을 이용한 데이터 교환

## 여기서 배우게 될 것

* WebRTC 종단간(peer끼리) 데이터를 교환하는 방법

전체 소스는 **step-03** 디렉토리에 있다.

## HTML 수정하기
이 장에서는 WebRTC 데이터 채널을 사용하여 동일한 페이지의 두 ```textarea``` 요소간에 텍스트를 보낸다. 그다지 유용하지는 않지만 WebRTC를 사용하여 스트리밍 비디오와 데이터를 공유하는 방법을 보여준다.

**index.html** 에서 비디오 및 버튼 요소를 제거하고 다음 HTML로 수정 해 보자.
``` javascript
<textarea id="dataChannelSend" disabled
    placeholder="Press Start, enter some text, then press Send."></textarea>
<textarea id="dataChannelReceive" disabled></textarea>

<div id="buttons">
  <button id="startButton">Start</button>
  <button id="sendButton">Send</button>
  <button id="closeButton">Stop</button>
</div>
```

하나의 텍스트 영역은 텍스트를 입력하기위한 것이며 다른 하나는 peer끼리 스트리밍 된 텍스트를 표시한다.

수정한 index.html은 다음과 같다.
``` javascript
<!DOCTYPE html>
<html>

<head>

  <title>Realtime communication with WebRTC</title>

  <link rel="stylesheet" href="css/main.css" />

</head>

<body>

  <h1>Realtime communication with WebRTC</h1>

  <textarea id="dataChannelSend" disabled
    placeholder="Press Start, enter some text, then press Send."></textarea>
  <textarea id="dataChannelReceive" disabled></textarea>

  <div id="buttons">
    <button id="startButton">Start</button>
    <button id="sendButton">Send</button>
    <button id="closeButton">Stop</button>
  </div>

  <script src="js/lib/adapter.js"></script>
  <script src="js/main.js"></script>

</body>

</html>
```

Javascript를 수정해 보자.

**main.js** 를 **step-03/js/main.js** 로 대체한다.

> 이 전 장에서와 마찬가지로 코드랩의 많은 코드들을 잘라내서 붙여넣기 하는것이 효율적인 방법은 아니지만(RTCPeerConnection 처럼) 실행하기 위해선 딱히 대안이 없다.

peer간 데이터 스트리밍을 시도해 보자: **index.html** 을 열고 **Start** 를 눌러 peer 연결을 설정하고 왼쪽의 텍스트 영역에 텍스트를 입력 한 다음 **Send** 를 클릭하여 WebRTC 데이터 채널을 사용하여 텍스트를 전송해 보자.

## 어떻게 동작할까

이 코드는 RTCPeerConnection과 RTCDataChannel을 사용하여 문자 메시지를 교환한다.

이번 장의 많은 코드들이 RTCPeerConnection 예제와 동일하다.

```sendData()``` 및 ```createConnection()``` 함수는 대부분 새로운 코드들이다.
``` javascript
function createConnection() {
  dataChannelSend.placeholder = '';
  var servers = null;
  pcConstraint = null;
  dataConstraint = null;
  trace('Using SCTP based data channels');
  // For SCTP, reliable and ordered delivery is true by default.
  // Add localConnection to global scope to make it visible
  // from the browser console.
  window.localConnection = localConnection =
      new RTCPeerConnection(servers, pcConstraint);
  trace('Created local peer connection object localConnection');

  sendChannel = localConnection.createDataChannel('sendDataChannel',
      dataConstraint);
  trace('Created send data channel');

  localConnection.onicecandidate = iceCallback1;
  sendChannel.onopen = onSendChannelStateChange;
  sendChannel.onclose = onSendChannelStateChange;

  // Add remoteConnection to global scope to make it visible
  // from the browser console.
  window.remoteConnection = remoteConnection =
      new RTCPeerConnection(servers, pcConstraint);
  trace('Created remote peer connection object remoteConnection');

  remoteConnection.onicecandidate = iceCallback2;
  remoteConnection.ondatachannel = receiveChannelCallback;

  localConnection.createOffer().then(
    gotDescription1,
    onCreateSessionDescriptionError
  );
  startButton.disabled = true;
  closeButton.disabled = false;
}

function sendData() {
  var data = dataChannelSend.value;
  sendChannel.send(data);
  trace('Sent Data: ' + data);
}
```

RTCDataChannel의 문법은 ```send()```메서드나 ```message```이벤트들을 사용하여 의도적으로 WebSocket과 비슷하게 되어있다.

```dataConstraint```의 사용에 주목하자. 데이터 채널은 다양한 유형의 데이터 공유가 가능하도록 구성 할 수 있다(예: 성능보다 안정적인 전송을 우선 순위로 지정). [Mozilla Developer Network](https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/createDataChannel) 에서 옵션에 대한 자세한 정보를 찾을 수 있다.