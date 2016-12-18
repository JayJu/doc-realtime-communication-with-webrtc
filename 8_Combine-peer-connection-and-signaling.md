# 8. peer 커넥션과 시그널링 결합하기

## 여기서 배우게 될 것

* Node.js에서 실행중인 Socket.IO를 사용하여 WebRTC 시그널링 서비스를 하는 방법
* 이 서비스로 피어간에 WebRTC 메타 데이터를 교환하는 방법

전체 소스는 **step-05** 디렉토리에 있다.

## HTML과 Javascript 수정
**index.html** 의 내용을 다음과 같이 변경한다.

``` javascript
<!DOCTYPE html>
<html>

<head>

  <title>Realtime communication with WebRTC</title>

  <link rel="stylesheet" href="/css/main.css" />

</head>

<body>

  <h1>Realtime communication with WebRTC</h1>

  <div id="videos">
    <video id="localVideo" autoplay muted></video>
    <video id="remoteVideo" autoplay></video>
  </div>

  <script src="/socket.io/socket.io.js"></script>
  <script src="js/lib/adapter.js"></script>
  <script src="js/main.js"></script>
  
</body>

</html>
```

**js/main.js** 를 **step-05/js/main.js** 의 내용으로 바꾼다.

## Node 서버 실행
노드 서버가 실행되고 있지 않으면 **작업** 디렉토리에서 다음 명령을 호출하여 노드 서버를 시작하십시오.

``` shellscript
node index.js
```

(이전 장에서 Socket.IO를 구현 한 index.js 버전을 사용하고 있는지 확인하자)

브라우저에서 **localhost:8080** 으로 접속한다.

새 탭 또는 창에서 **localhost:8080** 로 다시 접속한다. 하나의 비디오 요소는 ```getUserMedia()```에서 로컬 스트림을 표시하고 다른 하나는 RTCPeerconnection을 통해 스트리밍 된 '원격'비디오를 표시한다.

> 클라이언트 탭이나 창을 닫을 때마다 노드 서버를 다시 시작해야 한다.

브라우저 콘솔에서 로깅을 확인한다.

## 보너스 점수

1. 이 응용 프로그램은 일대일 화상 채팅만 지원한다. 두 명 이상이 같은 화상 채팅방을 공유하려면 어떻게 변경해야 할까?

2. 이 예제는 방 이름 foo가 하드 코딩되어 있다. 다른 방 이름을 사용하는 가장 좋은 방법은 무엇인가?

3. 사용자가 객실 이름 공유하려면 어떻게 해야 할까? 방 이름을 공유하는 대안을 알아보자.

4. 앱을 어떻게 바꿀 수 있을까?

## 지금까지 배운 것들

이번 장에서는

* Node.js에서 실행중인 Socket.IO를 사용하여 WebRTC 시그널링 서비스를 하는 방법
* 이 서비스로 피어간에 WebRTC 메타 데이터를 교환하는 방법

에 대해 배웠다. 전체 소스는 **step-05** 디렉토리에 있다.

## 팁

* WebRTC 통계 및 디버그 데이터는 **chrome://webrtc-internals** 에서 확인할 수 있다.
* [test.webrtc.org](https://test.webrtc.org/)를 사용하여 로컬 환경을 확인하고 카메라와 마이크를 테스트 할 수 있다.
* 캐싱에 이상한 점이 있으면 다음과 같이 수행해보자.
  * ctrl 키를 누른 상태에서 새로 고침 버튼을 클릭하여 강제 **새로고침** 한다.
  * 브라우저를 재시작한다.
  * 명령 행에서 ```npm cache clean```을 실행한다.

## 다음단계
사진을 찍고, 이미지 데이터를 얻고, 원격 피어끼리 공유하는 방법을 알아보자.
