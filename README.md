<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Face Recognition App</title>
    <link rel="stylesheet" href="styles.css">
    <script defer src="https://cdn.jsdelivr.net/npm/face-api.js"></script>
    <script defer src="app.js"></script>
</head>
<body>
    <h1>Face Recognition App</h1>
    <video id="video" autoplay muted></video>
    <canvas id="canvas" style="position: absolute;"></canvas>
</body>
</html>

body {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    margin: 0;
    background-color: #f0f0f0;
}

h1 {
    color: #333;
}

#video {
    width: 640px;
    height: 480px;
    border: 2px solid #333;
}

canvas {
    position: absolute;
    top: 0;
    left: 0;
}

const video = document.getElementById('video');
const canvas = document.getElementById('canvas');
const ctx = canvas.getContext('2d');

// Load models from face-api.js
async function loadModels() {
    const MODEL_URL = '/models'; // Assuming models are stored in a folder named 'models'
    await faceapi.nets.tinyFaceDetector.loadFromUri(MODEL_URL);
    await faceapi.nets.faceLandmark68Net.loadFromUri(MODEL_URL);
    await faceapi.nets.faceRecognitionNet.loadFromUri(MODEL_URL);
}

// Start the video stream
async function startVideo() {
    const stream = await navigator.mediaDevices.getUserMedia({ video: {} });
    video.srcObject = stream;
}

// Detect faces and draw on canvas
async function detectFace() {
    const detection = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions()).withFaceLandmarks().withFaceDescriptors();
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    canvas.width = video.videoWidth;
    canvas.height = video.videoHeight;
    
    // Draw detections
    faceapi.draw.drawDetections(canvas, detection);
    faceapi.draw.drawFaceLandmarks(canvas, detection);
    
    requestAnimationFrame(detectFace);
}

// Initialize the application
(async () => {
    await loadModels();
    await startVideo();
    detectFace();
})();
