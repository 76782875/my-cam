<template>
  <div id="photo" style="width: 100vw;height: 100vh">

    <svg class="svg" width="100%" style="position: absolute;height: 100vh;z-index: 100">
      <defs>
        <mask id="myMask">
          <rect x="0" y="0" width="100vw" height="100vh" style="fill: #ccc"></rect>
          <rect x="20%" y="25%" width="60vw" height="60vw" stroke-opacity="1" stroke-width="2"
                style="fill: #000;stroke: aqua;"></rect>
        </mask>
      </defs>
      <rect x="0" y="0" width="100%" height="100%"
            style="stroke: none; fill: rgba(0, 0, 0, 0.6); mask: url(#myMask)"></rect>
    </svg>
<!--    <button id="btn" style="position: absolute;z-index: 1000" @click="screenshot">点我截图</button>-->
    <button id="btn_cancel" style="position: absolute;z-index: 1000;top: 5vh" @click="cancel">取消截图</button>
    <canvas id="cav"
            style="position: absolute;width: 60vw;height: 60vw;transform: translate(20vw,25vh);z-index: 999;"></canvas>
    <video id="video" autoplay playsinline class="bg" ref="video"
           style="position: absolute;left: 50%;transform: translate(-50%,0)"></video>
<!--    <input type="file" accept="image/*" capture="camera" style="position: absolute;z-index: 1002" />-->

  </div>

</template>

<script>
export default {
  name: 'HelloWorld',
  mounted () {
    this.init()
  },
  methods: {
    init () {
      const video = this.$refs.video
      // 一堆兼容代码
      window.URL = (window.URL || window.webkitURL || window.mozURL || window.msURL)
      if (navigator.mediaDevices === undefined) {
        navigator.mediaDevices = {}
      }
      if (navigator.mediaDevices.getUserMedia === undefined) {
        navigator.mediaDevices.getUserMedia = function (constraints) {
          var getUserMedia = navigator.webkitGetUserMedia || navigator.mozGetUserMedia || navigator.msGetUserMedia
          if (!getUserMedia) {
            return Promise.reject(new Error('getUserMedia is not implemented in this browser'))
          }
          return new Promise(function (resolve, reject) {
            getUserMedia.call(navigator, constraints, resolve, reject)
          })
        }
      }
      // 摄像头调用配置
      const mediaOpts = {
        audio: false,
        video: { facingMode: 'user' } // 或者 "user"
        // video: { width: 1280, height: 720 }
        // video: { facingMode: { exact: "environment" } }// 或者 "user"
      }
      navigator.mediaDevices.getUserMedia(mediaOpts).then(stream => {
        if ('srcObject' in video) {
          video.srcObject = stream
        } else {
          video.src = window.URL || window.URL.createObjectURL(stream) || stream
        }
        video.play()
      }).catch(err => {
        alert(err.name)
      })
    },
    screenshot () {
      const canvas = document.getElementById('cav')
      const video = document.querySelector('video')

      const ctx = canvas.getContext('2d')
      const aspect = window.innerWidth / window.innerHeight
      const tmpWidth = video.videoHeight * aspect
      const x = (video.videoWidth - tmpWidth) / 2 + tmpWidth * 0.2
      const y = video.videoHeight * 0.25
      ctx.drawImage(video, x, y, tmpWidth * 0.6, tmpWidth * 0.6, 0, 0, canvas.width, canvas.height)
    },
    cancel () {
      const canvas = document.getElementById('cav')
      const ctx = canvas.getContext('2d')
      ctx.clearRect(0, 0, canvas.width, canvas.height)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style>

  @media screen and (orientation: landscape) {
    #video{
      width : 100vw;
      height : 100vh;
    }
  }
</style>
