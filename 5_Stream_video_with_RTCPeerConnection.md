# 5. RTCPeerConnection을 이용한 동영상 스트리밍

## 여기서 배우게 될 것
* [adapter.js](https://github.com/webrtc/adapter)로 브라우저간 WebRTC 차이 메꾸기
* RTCPeerConneciton API를 이용해 동영상 스트리밍 하기
* 미디어 캡쳐와 스트리밍 제어하기

## RTCPeerConnection 이란?
RTCPeerConnection은 동영상과 음성, 그리고 데이터를 주고 받을 수 있도록 WebRTC를 구성하는 API 이다. 다음은 동일 페이지에서 두 개의 RTCPeerConnection 객체(peer로 불리는)끼리 연결을 맺는 예제이다. 
효율적인 방식은 아니지만 RTCPeerConnection이 어떻게 동작하는지 이해하는 데 좋다. 

## video요소와 컨트롤 버튼 추가하기
**index.html** 에 있던 하나의 video 요소를 두개로 늘리고 버튼 3개를 추가한다.

``` html
<video id="localVideo" autoplay></video>
<video id="remoteVideo" autoplay></video>

<div>
 <button id="startButton">Start</button>
 <button id="callButton">Call</button>
 <button id="hangupButton">Hang Up</button>
</div>

```

한 video 요소는 `getUserMedia()`로 부터 동영상 스트림을 출력하고 다른 하나는 RTCPeerConnection을 통해 동일 동영상을 출력한다. (현실에서는 한쪽은 로컬, 다른 한쪽은 리모트에서 스트리밍된다)

## adapter.js 끼워넣기
**main.js** 에 **adapter.js** 링크를 추가한다.

``` javascript
<script src="js/lib/adapter.js"></script>
```