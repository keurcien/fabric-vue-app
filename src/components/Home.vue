<template>
    <div>
        
        <h1>Simple Vue App</h1>

        <div class="camera">
            <video id="video" autoplay></video>
        </div>

        <button @click="start">Start</button>
        <button @click="stop">Stop</button>
        <button @click="getImageMatrixFromVideo">Snap</button>

        <canvas id="canvas" hidden></canvas>

    </div>
</template>

<script>
export default {
    data() {
        return {
            imageMatrix: null
        }
    },
    methods: {

        start() {
            // Check whether the browser supports video recording
            if ("mediaDevices" in navigator && "getUserMedia" in navigator.mediaDevices) {
                navigator.mediaDevices.getUserMedia({video: {facingMode: "environment"}}).then(stream => {
                    // Stream the video within the player 
                    const videoPlayer = document.querySelector("video")
                    videoPlayer.srcObject = stream
                })
            }
        },
        stop() {
            const videoPlayer = document.querySelector("video")
            const stream = videoPlayer.srcObject
            if (videoPlayer.srcObject) {
                const tracks = stream.getTracks()
                tracks.forEach(track => { track.stop() })
                videoPlayer.srcObject = null
            }
        },
        getImageMatrixFromVideo() {
            const videoPlayer = document.querySelector("video")
            
            // Get the current frame from the video and store it in a canvas object
            if (videoPlayer.srcObject) {
                const canvas = document.querySelector("canvas")
                canvas.width  = videoPlayer.videoWidth
                canvas.height  = videoPlayer.videoHeight
                let context = canvas.getContext('2d')
                context.drawImage(videoPlayer, 0, 0, canvas.width, canvas.height)

                // Convert the resulting image to an array
                const imageMatrix = context.getImageData(0, 0, canvas.width, canvas.height)
                this.imageMatrix = imageMatrix
            }
        }
    }
}
</script>

<style>
.camera {
    height: 500px;
}
</style>