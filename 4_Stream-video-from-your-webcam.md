# 4. 웹캠으로 동영상 스트리밍하기

## 여기서 배우게 될 것
* 웹캠을 사용해 동영상 스트림 얻기
* 스트림 전송 다루기
* 동영상을 다루기 위한 CSS 와 SVG 사용하기

이번 단계의 전체 소스는 **step-01** 폴더에 있다.

## HTML 맛보기
**작업디렉토리** 내 **index.html**에 `video` 와 `script`엘리먼트를 추가한다.

```html
<!DOCTYPE html>
<html>

<head>

 <title>Realtime communication with WebRTC</title>

 <link rel="stylesheet" href="css/main.css" />

</head>

<body>

 <h1>Realtime communication with WebRTC</h1>

 <video autoplay></video>

 <script src="js/main.js"></script>

</body>

</html>
```