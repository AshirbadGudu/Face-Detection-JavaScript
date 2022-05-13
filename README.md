# Face Detection In JavaScript

## Steps

### Access all the required dom elements

```js
const video = document.getElementById("video");
const mainContainer = document.getElementById("main-container");
```

### Access the webcam

```js
const startVideo = async () => {
  try {
    const stream = await navigator.mediaDevices.getUserMedia({
      video: true,
      audio: false,
    });
    video.srcObject = stream;
  } catch (error) {
    console.log(error);
  }
};
```

### Once all the models are loaded, start the video

```js
Promise.all([
  faceapi.nets.tinyFaceDetector.loadFromUri("../models"),
  faceapi.nets.faceLandmark68Net.loadFromUri("../models"),
  faceapi.nets.faceRecognitionNet.loadFromUri("../models"),
  faceapi.nets.faceExpressionNet.loadFromUri("../models"),
]).then(startVideo);
```

### Once video start playing, start the detection

```js
video.addEventListener("play", () => {
  const canvas = faceapi.createCanvasFromMedia(video);
  canvas.classList.add("absolute");
  canvas.classList.add("inset-0");
  mainContainer.append(canvas);
  const displaySize = { width: video.width, height: video.height };
  faceapi.matchDimensions(canvas, displaySize);
  setInterval(async () => {
    const detections = await faceapi
      .detectAllFaces(video, new faceapi.TinyFaceDetectorOptions())
      .withFaceLandmarks()
      .withFaceExpressions();
    const resizedDetections = faceapi.resizeResults(detections, displaySize);
    canvas.getContext("2d").clearRect(0, 0, canvas.width, canvas.height);
    faceapi.draw.drawDetections(canvas, resizedDetections);
    faceapi.draw.drawFaceLandmarks(canvas, resizedDetections);
    faceapi.draw.drawFaceExpressions(canvas, resizedDetections);
  }, 100);
});
```
