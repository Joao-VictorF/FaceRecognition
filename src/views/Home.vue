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

        <video
          v-show="videoStarted"
          id="video"
          class="mx-auto h-72 w-auto"
          autoplay
          muted
        >
        </video>
      </div>

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
import { ref } from 'vue'

export default {
  name: 'Home',
  setup () {
    var videoStarted = ref(false)

    function startVideo () {
      videoStarted.value = !videoStarted.value
      const video = document.getElementById('video')
      navigator.getUserMedia(
        { video: {} },
        (stream) => {
          video.srcObject = stream
        },
        err => console.log(err)
      )
    }
    return { videoStarted, startVideo }
  }
}
</script>

<style scoped>
  .background-color {
    background-color: #edf4f8;
  }
</style>
