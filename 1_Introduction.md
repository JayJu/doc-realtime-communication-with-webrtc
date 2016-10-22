# 1. 소개

WebRTC는 웹과 네이티브 앱에서 음성과 영상, 그리고 데이터를 실시간으로 주고 받을 수 있게 하는 오픈소스 프로젝트이다. 
WebRTC는 몇 가지의 자바스크립트 API로 구성된다\(링크를 클릭하면 데모를 볼 수 있다\).

* [getUserMedia\(\)](https://webrtc.github.io/samples/src/content/getusermedia/gum/): 음성과 영상 가져오기

* [MediaRecorder](https://webrtc.github.io/samples/src/content/getusermedia/record/): 음성과 영상 녹화하기

* [RTCPeerConnection](https://webrtc.github.io/samples/src/content/peerconnection/pc1/): 유저 간 음성과 영상 전송하기

* [RTCDataChannel](https://webrtc.github.io/samples/src/content/datachannel/basic/): 유저 간 데이터 전송하기


## WebRTC는 어디서 사용할 수 있나

데스크탑이나 안드로이드에서 파이어폭스, 오페라, 크롬 브라우저로 가능하다. 또한 iOS와 안드로이드의 네이티브 앱에서도 가능하다.

## 시그널링\(signaling\)이란?

WebRTC는 브라우저 사이의 데이터 스트림을 주고 받기 위해 필요한 코디네이션 메커니즘과 제어메시지 전달을 위해 RTCPeerConnection API를 사용하는데 이러한 프로세스를 시그널링\(signaling\)이라 한다. 시그널링의 메서드와 규약은 WebRTC에 정의되어 있지 않다. 여기서는 Node를 사용하지만 [다른 대안들](https://github.com/muaz-khan/WebRTC-Experiment/blob/master/Signaling.md)도 많다.

## STUN과 TURN 이란?

WebRTC는 유저들이 가장 가까운 라우터로 연결할 수 있도록 peer-to-peer 기반으로 설계되었으며 현실 네트워킹\(NAT나 방화벽 등\)에 잘 대응할 수 있도록 구현되어 있다. 이러한 프로세스의 한 부분으로 WebRTC는 유저 컴퓨터의 IP주소를 가져오는 STUN서버와 p2p 연결이 실패할 경우를 대비해 릴레이 서버 기능을 하는 TURN 서버를 사용한다. \(상세설명은 [이곳](https://www.html5rocks.com/en/tutorials/webrtc/infrastructure/)을 참고\)

## WebRTC는 안전한가?

모든 WebRTC 컴포넌트들에 대해 암호화는 강제사항이며 Javascript API들은 출처가 안전한 경우\(HTTPS 또는 localhost\)에만 사용할 수 있다. 시그널링 매커니즘은 WebRTC 표준에 정의되어 있지 않으므로 안전한 프로토콜을 확인하는 것은 당신이 하기에 달려있다.

