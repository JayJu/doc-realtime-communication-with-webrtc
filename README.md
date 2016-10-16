# 1. 소개

WebRTC는 웹과 네이티브 앱에서 음성과 영상, 그리고 데이터를 실시간으로 주고 받을 수 있게 하는 오픈소스 프로젝트이다. WebRTC는 몇개의 자바스크립트 API로 구성된다(링크를 클릭하면 데모를 볼 수 있다).

* [getUserMedia\(\)](https://webrtc.github.io/samples/src/content/getusermedia/gum/): 음성과 영상 캡쳐하기

* [MediaRecorder](https://webrtc.github.io/samples/src/content/getusermedia/record/): 음성과 영상 녹화하기

* [RTCPeerConnection](https://webrtc.github.io/samples/src/content/peerconnection/pc1/): 유저 간 음성과 영상 전송하기

* [RTCDataChannel](https://webrtc.github.io/samples/src/content/datachannel/basic/): 유저 간 데이터 전송하기

## WebRTC는 어디서 사용할 수 있나

데스크탑이나 안드로이드에서 파이어폭스, 오페라, 크롬 브라우저로 가능하다. 또한 iOS와 안드로이드의 네이티브 앱에서도 가능하다.

## 시그널링\(signaling\)이란?

WebRTC는 브라우저 사이의 데이터 스트림을 주고 받기 위해 필요한 코디네이션 메커니즘과 제어메시지 전달을 위해 RTCPeerConnection API를 사용하며 이것이 시그널링\(signaling\)으로 알려진 프로세스 이다. 시그널링 메서드와 프로토콜은 WebRTC에 정의되어 있지 않다. 여기서는 Node를 사용하지만 [다른 대안들](https://github.com/muaz-khan/WebRTC-Experiment/blob/master/Signaling.md)도 많다.

## STUN과 TURN 이란?

WebRTC는 peer-to-peer 기반으로 설계되어 유저는 가장 가까운 라우터로 연결할 수 있다. 하지만 WebRTC는 현실 네트워킹에 잘 대응할 수 있도록 구현되어 있다.

## WebRTC는 안전한가?
