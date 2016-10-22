# 5. RTCPeerConnection을 이용한 동영상 스트리밍

## 여기서 배우게 될 것

* [adapter.js](https://github.com/webrtc/adapter)로 브라우저간 WebRTC 차이 메꾸기
* RTCPeerConneciton API를 이용해 동영상 스트리밍 하기
* 미디어 캡쳐와 스트리밍 제어하기

## RTCPeerConnection 이란?

RTCPeerConnection은 동영상과 음성, 그리고 데이터를 주고 받을 수 있도록 WebRTC를 구성하는 API 이다. 다음은 동일 페이지에서 두 개의 RTCPeerConnection 객체\(peer로 불리는\)끼리 연결을 맺는 예제이다. 
효율적인 방식은 아니지만 RTCPeerConnection이 어떻게 동작하는지 이해하는 데 좋다.

## video요소와 컨트롤 버튼 추가하기

**index.html** 에 있던 하나의 video 요소를 두개로 늘리고 버튼 3개를 추가한다.

```html
<video id="localVideo" autoplay></video>
<video id="remoteVideo" autoplay></video>

<div>
 <button id="startButton">Start</button>
 <button id="callButton">Call</button>
 <button id="hangupButton">Hang Up</button>
</div>

```

한 video 요소는 `getUserMedia()`로 부터 동영상 스트림을 출력하고 다른 하나는 RTCPeerConnection을 통해 동일 동영상을 출력한다. \(현실에서는 한쪽은 로컬, 다른 한쪽은 리모트에서 스트리밍된다\)

## adapter.js 끼워넣기

**main.js** 에 **adapter.js** 링크를 추가한다.

```javascript
<script src="js/lib/adapter.js"></script>
```

> **adapter.js**는 스펙의 변경이나 접두어의 차이로부터 앱을 보호하기 위한 일종의 쐐기이다.
> 
> 사실, WebRTC 구현체에서 사용하는 표준과 규약들은 매우 안정적이며 접두어가 붙은 이름들도 많지 않다.
> 
> 이 장에서는 **adapter.js**의 최신버전의 로컬사본을 연결하였다. - 연습용으로는 괜찮으나 운영서버에 적용하는것은 적절치 않다. [GitHub 레파지토리의 adapter.js ](https://github.com/webrtc/adapter)는 항상 최신버전의 기능에 대해서만 기술한다.
> 
> WebRTC의 상호운영성에\(interop\) 대한 모든 정보는 [webrtc.org\/web-apis\/interop](https://webrtc.org/web-apis/interop/)에서 확인 할 수 있다.

**index.html**은 아래와 같은 모습이 될 것이다.

```javascript
<!DOCTYPE html><html>

<head>

 <title>Realtime communication with WebRTC</title>

 <link rel="stylesheet" href="css/main.css" />

</head>

<body>

 <h1>Realtime communication with WebRTC</h1>

 <video id="localVideo" autoplay></video> 
 <video id="remoteVideo" autoplay></video>

 <div>
   <button id="startButton">Start</button> 
   <button id="callButton">Call</button> 
   <button id="hangupButton">Hang Up</button> 
 </div>

 <script src="js/lib/adapter.js"></script>
 <script src="js/main.js"></script>

</body>

</html>

```

## RTCPeerConnection 코드 설치하기

**main.js** 파일을 **step-02** 폴더의 버전으로 변경한다.

> 코드랩의 많은 코드들을 잘라내서 붙여넣기 하는것이 효율적인 방법은 아니지만 RTCPeerConnection을 실행하기 위해선 딱히 대안이 없다.
> 
> 이제 이 코드들이 어떻게 동작하는지 알아보자.

## 요청\(call\) 만들기

**index.html** 을 띄우고 웹캠에서 영상을 가져오기 위해 **start** 버튼을 클릭한다. 그런 다음 peer 커넥션을 생성하기 위해 **Call** 버튼을 클릭한다. 두개의 video에 동일한 영상이 보일 것이다. 브라우져 콘솔을 열어 WebRTC 로그글을 확인하자.

## 어떻게 동작할까

이번장은 꽤 많은 것들에 대해 알아야 한다.

> 이 부분을 건너 뛰고 싶다면, 그렇게 해도 상관 없다.
> 코드랩을 계속 하는 데 지장은 없다.

WebRTC는 **peer**라 부르는 WebRTC 클라이언트들 끼리 영상을 스트리밍 하기 위해 RTCPeerConnection 이라는 API를 사용하여 커넥션을 맺는다.

이 예제에서는 같은 페이지에 두 개의 RTCPeerConnection 객체\(`pc1`과`pc2`\)가 존재한다. 실용적인 방법은 아니지만 API가 어떻게 동작하는지 살펴보기엔 적당하다.

WebRTC peer들 간 call을 설정하기 위해선 3단계의 작업이 필요하다.

* RTCPeerConnection을 생성한 후 `getUserMedia()`로 가져온 로컬 스트림을 추가한다.
* 네크워크 정보\([ICE](https://en.wikipedia.org/wiki/Interactive_Connectivity_Establishment) 후보라 부르는 연결 가능한 단말\)를 가져와 공유한다. 
* 로컬과 리모트 정보\([SDP](https://en.wikipedia.org/wiki/Session_Description_Protocol) 포맷의 로컬 미디어 메타정보\)들을 가져와 공유한다: 

앨리스와 밥이 화상채팅을 하기 위해 RTCPeerConnection을 사용한다고 가정하자.

처음에 할 일은 서로의 네트워크 정보를 교환하는 것이다. '후보들을 찾는다' 라는 표현은 [ICE](https://en.wikipedia.org/wiki/Interactive_Connectivity_Establishment) 프레임워크를 사용하여 네트워크 인터페이스와 포트들을 찾는 절차를 의미한다.

1\) 앨리스는 RTCPeerConnection 객체를 생성하고 `onicecandidate` 이벤트 핸들러를 등록한다. **main.js**의 아래 코드블럭에 해당한다.

```javascript
pc1 = new RTCPeerConnection(servers); 
trace('Created local peer connection object pc1'); 
pc1.onicecandidate = function(e) {   
  onIceCandidate(pc1, e); 
};

```

> 이번 예제에서는 RTCPeerConnection 의 `servers` 인자를 사용하지 않았다.
> 
> 이 부분은 STUN 과 TURN 서버들을 지정할 때 사용한다.
> 
> WebRTC는 유저들이 최단경로로 연결될 수 있도록 peer-to-peer 기반으로 설계되었지만 또한 현실 네트워킹\([NAT게이트웨이](https://en.wikipedia.org/wiki/NAT_traversal)나 방화벽 등\)에 잘 대응할 수 있도록 구현되어 있다.
> 
> 이 절차의 한 부분으로 WebRTC API들은 당신 컴퓨터의 IP주소를 가져오기 위해 STUN 서버를 사용하고 peer-to-peer 연결이 실패할 경우 를 대비해 릴레이 서버 역할을 할 수 있도록 TURN 서버를 사용한다. 상세한 정보는 [WebRTC in the real world](https://www.html5rocks.com/en/tutorials/webrtc/infrastructure)애서 확인 할 수 있다.

2\) 앨리스는 `getUserMedia()`를 호출하고 전달받은 스트림을 다음과 같이 추가한다.

```javascript
pc1.addStream(localStream);
```

3\) 1단계의 `onicecandiate` 핸들러는 네트워크 후보들이 연결 가능한 상태가 될 때 호출된다.

4\) 앨리스는 직렬화된 후보 데이터를 밥에게 보낸다. 실제 어플리케이션에서 이 절차\(**시그널링**으로 알려진\)는 메시징 서비스\(다음 단계에서 알아보도록 한다.\)를 통해 이루어진다. - 이 예제에서는RTCPeerConnection 객체들이 같은 페이지에 있기 때문에 외부 메시지는 필요하지 않다)

5) 밥은 앨리스로부터 후보 데이터를 받은 후 `addIceCandiate()`를 호출하여 후보 데이터를 리모트 peer 의 명세서에 추가한다.

``` javascript
function onIceCandidate(pc, event) { if (event.candidate) { getOtherPc(pc).addIceCandidate( new RTCIceCandidate(event.candidate) ).then( function() { onAddIceCandidateSuccess(pc); }, function(err) { onAddIceCandidateError(pc, err); } ); trace(getName(pc) + ' ICE candidate: \n' + event.candidate.candidate); }}

```

