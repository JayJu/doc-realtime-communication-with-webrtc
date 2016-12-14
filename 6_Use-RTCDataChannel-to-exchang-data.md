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

> **세 가지 타입의 제약 조건**
>
> 혼란스러울 수 있다!
>
> 서로 다른 유형의 WebRTC 호출에 대한 설정 옵션을 모두 '제약조건' 이라고 부르기도 한다.
>
> 제약 조건 및 옵션에 대해 자세히 알아보자: 
>
> * [RTCPeerConnection](https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/RTCPeerConnection)
> * [RTCDataChannel](https://developer.mozilla.org/en-US/docs/Web/API/RTCPeerConnection/createDataChannel)
> * [getUserMedia()](https://developer.mozilla.org/en-US/docs/Web/API/MediaDevices/getUserMedia)

## 보너스 점수
1. WebRTC 데이터 채널에서 사용하는 프로토콜 인 [SCTP](https://bloggeek.me/sctp-data-channel/)를 사용하면 신뢰할 수 있고 순차적인 데이터 전달이 기본적(default)으로 설정된다. RTCDataChannel이 안정적인 데이터 전달을 우선해야 하는 경우는 언제이며 (데이터가 손실된다 하더라도)성능이 우선해야 하는 경우는 언제일까?

2. CSS를 사용하여 페이지 레이아웃을 개선하고 textarea의 placeholder 속성에 "dataChannelReceive"를 추가해 보자.

3. 모바일 장치에서 페이지를 테스트해 보자.

## 지금까지 배운 것들

이번 장에서는

* 두 WebRTC peer간의 연결을 설정하고
* peer간에 텍스트 데이터를 교환하는 방법

에 대해 배웠다. 전체 소스는 **step-03** 디렉토리에 있다.

## 좀 더 찾아보기
* [WebRTC 데이터 채널들](https://www.html5rocks.com/en/tutorials/webrtc/datachannels/)(수 년이 지났으나 여전히 읽어볼 가치가 있다)
* [WebRTC의 데이터 채널로 SCTP가 선택된 이유는 무엇일까?](https://bloggeek.me/sctp-data-channel/)

## 다음단계
같은 페이지에있는 peer끼리 데이터를 교환하는 방법을 배웠지만 다른 컴퓨터끼리는 어떻게 해야할까? 먼저 메타 데이터 메시지를 교환하기 위해 시그널링 채널을 설정해야한다. 다음 단계에서 어떻게 되는지 알아보자!