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
> **adapter.js**는 스펙의 변경이나 접두어의 차이로부터 앱을 보호하기 위한 일종의 쐐기이다. 

> 사실, WebRTC 구현체에서 사용하는 표준과 규약들은 매우 안정적이며 접두어가 붙은 이름들도 많지 않다.

> 이 장에서는 **adapter.js**의 최신버전의 로컬사본을 연결하였다. - 연습용으로는 괜찮으나 운영서버에 적용하는것은 적절치 않다. [GitHub 레파지토리의 adapter.js ](https://github.com/webrtc/adapter)는 항상 최신버전의 기능에 대해서만 기술한다.

> WebRTC의 상호운영성에(interop) 대한 모든 정보는 [webrtc.org/web-apis/interop](https://webrtc.org/web-apis/interop/)에서 확인 할 수 있다.
