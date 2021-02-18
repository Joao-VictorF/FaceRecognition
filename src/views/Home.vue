<template>
  <div class="min-h-screen flex items-center justify-center bg-gray-50 py-12 px-4 sm:px-6 lg:px-8 background-color">
    <div class="max-w-md w-full space-y-8">
      <div>
        <!-- <img class="mx-auto h-12 w-auto" src="https://tailwindui.com/img/logos/workflow-mark-indigo-600.svg" alt="Workflow"> -->
        <h2 class="mt-6 text-center text-3xl font-extrabold text-gray-900">
          Face detection
        </h2>
      </div>

      <div>
        <img
          v-if="!videoStarted"
          class="mx-auto h-72 w-auto"
          src="@/assets/face_recognition.gif"
          alt="Face detection animation"
        >

        <div
          v-show="videoStarted"
        >
          <video
            id="video"
            autoplay
            muted
          >
          </video>
          <canvas id="canvas"></canvas>
        </div>
      </div>
      <br>

      <div>
        <button
          class="group relative w-full flex justify-center py-2 px-4 border border-transparent text-sm font-medium rounded-md text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
          @click="startVideo()"
        >
          Scan my face
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
const faceapi = require('face-api.js')

export default {
  name: 'Home',
  setup () {
    var videoStarted = ref(false)

    function loadModels () {
      return Promise.all([
        faceapi.nets.tinyFaceDetector.loadFromUri('/models'),
        faceapi.nets.faceLandmark68Net.loadFromUri('/models'),
        faceapi.nets.faceRecognitionNet.loadFromUri('/models'),
        faceapi.nets.faceExpressionNet.loadFromUri('/models')
      ])
    }

    async function startVideo () {
      videoStarted.value = !videoStarted.value
      const video = document.getElementById('video')

      await loadModels()
        .then(() => {
          navigator.getUserMedia(
            { video: {} },
            (stream) => {
              video.srcObject = stream
            },
            err => console.log(err)
          )
        })

      video.addEventListener('playing', () => {
        // const canvas = faceapi.createCanvasFromMedia(video)
        const canvas = document.getElementById('canvas')
        // document.body.append(canvas)
        const canvasSize = {
          width: video.offsetWidth,
          height: video.offsetHeight
        }

        canvas.width = canvasSize.width
        canvas.height = canvasSize.height

        canvas.style.top = `${(canvasSize.height / 2) - 12}px`

        faceapi.matchDimensions(canvas, canvasSize)

        setInterval(async () => {
          const detections = await faceapi.detectAllFaces(video, new faceapi.TinyFaceDetectorOptions())
            .withFaceLandmarks()
            .withFaceExpressions()

          const resizedDetections = faceapi.resizeResults(detections, canvasSize)
          canvas.getContext('2d').clearRect(0, 0, canvasSize.width, canvasSize.height)

          faceapi.draw.drawDetections(canvas, resizedDetections)
          faceapi.draw.drawFaceLandmarks(canvas, resizedDetections)
          faceapi.draw.drawFaceExpressions(canvas, resizedDetections)
        }, 100)
      })
    }

    onMounted(() => {
      loadModels()
    })
    return { videoStarted, startVideo, loadModels }
  }
}
</script>

<style scoped>
  .background-color {
    background-color: #edf4f8;
  }

  /* #video {
    -webkit-transform: scaleX(-1);
    transform: scaleX(-1);
  } */

  #canvas {
    border: 1px solid red;
    /* max-width: 100vw; */
    position: absolute;
  }
</style>
