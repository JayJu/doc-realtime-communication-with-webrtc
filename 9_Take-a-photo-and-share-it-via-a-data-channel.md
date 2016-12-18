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

## 보너스 점수
1. a

## 지금까지 배운 것들

이번 장에서는

* a

에 대해 배웠다. 전체 소스는 **step-03** 디렉토리에 있다.

## 좀 더 찾아보기
* 