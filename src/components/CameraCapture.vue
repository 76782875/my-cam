<template>
  <div class="camera-container">
    <a class="back-img" @click="hideCapture" style="position: absolute;z-index: 109;margin-left: 5vw;margin-top: 2vh;border: 2px solid #409eff;color: #409eff;border-radius: 5px;padding: 6px">
      `返回`
    </a>
    <svg class="svg">
      <defs>
        <mask id="myMask">
          <rect x="0" y="0" width="100%" height="100%" style="fill: #fff"></rect>
          <rect ref="maskRect" class="mask-rect"></rect>
        </mask>
      </defs>
      <rect x="0" y="0" width="100vw" height="100vh"
            style="stroke: none; fill: rgba(255, 255, 255, 0.9); mask: url(#myMask);"></rect>
    </svg>
    <video id="video" ref="video" autoplay playsinline></video>
    <canvas id="cav" ref="cav"></canvas>

    <div class="camera-actions">
      <button class="print-screen active print1" @click="confirmShoot">{{ shot ? '确认并上传' : '点击拍摄' }}</button>
      <button v-show="shot"  @click="cancelShot" class="print-screen">取消截图</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CameraCapture',
  props: {
    mode: {
      type: [Boolean, String],
      default: 'environment'
    }
  },
  mounted () {
    this.initCamera()
  },
  data: () => ({
    show: false,
    shot: false
  }),
  methods: {
    confirmShoot () {
      const canvas = this.$refs.cav

      if (this.shot) {
        this.$emit('hideCapture', canvas.toDataURL())
        return
      }
      this.shot = true
      const video = document.querySelector('video')

      const ctx = canvas.getContext('2d')
      const aspect = window.innerWidth / window.innerHeight

      let x = 0; let y = 0; let dx = 0; let dy = 0

      if (window.innerWidth < window.innerHeight) {
        let tmpWidth = video.videoHeight * aspect
        x = (video.videoWidth - tmpWidth) / 2 + tmpWidth * 0.1
        y = video.videoHeight * 0.15
        dx = tmpWidth * 0.8
        dy = video.videoHeight * 0.3
      } else {
        let tmp = video.videoWidth / aspect
        x = video.videoWidth * 0.15
        y = (video.videoHeight - tmp) / 2 + 0
        dx = video.videoWidth * 0.7
        dy = tmp * 1
      }

      ctx.drawImage(video, x, y, dx, dy, 0, 0, canvas.width, canvas.height)
      console.log()
    },
    cancelShot () {
      this.shot = false
      const canvas = this.$refs.cav
      const ctx = canvas.getContext('2d')
      ctx.clearRect(0, 0, canvas.width, canvas.height)
    },
    showCapture () {
      this.show = true
    },
    hideCapture () {
      this.$emit('hideCapture', undefined)
    },
    async initCamera () {
      const video = this.$refs.video
      // 一堆兼容代码
      window.URL = (window.URL || window.webkitURL || window.mozURL || window.msURL)
      if (navigator.mediaDevices === undefined) {
        navigator.mediaDevices = {}
      }
      if (navigator.mediaDevices.getUserMedia === undefined) {
        navigator.mediaDevices.getUserMedia = function (constraints) {
          let getUserMedia = navigator.webkitGetUserMedia || navigator.mozGetUserMedia || navigator.msGetUserMedia || navigator.getUserMedia
          if (!getUserMedia) {
            return Promise.reject(new Error('您的浏览器似乎无法拍照，请更换浏览器'))
          }
          return new Promise(function (resolve, reject) {
            getUserMedia.call(navigator, constraints, resolve, reject)
          })
        }
      }

      // 摄像头调用配置
      const mediaOpts = {
        audio: false,
        video: { facingMode: this.mode && this.mode === 'environment' ? { exact: 'environment' } : 'user'
        }// 或者 "user"
        // video: { width: 1280, height: 720 }
        // video: { facingMode: { exact: 'environment' } }// 或者 "user"
      }
      navigator.mediaDevices.getUserMedia(mediaOpts).then(stream => {
        if ('srcObject' in video) {
          video.srcObject = stream
        } else {
          video.src = window.URL || window.URL.createObjectURL(stream) || stream
        }
        video.play()
      }).catch(err => {
        alert(err.name + ': 您的浏览器不支持启用摄像头，请更换浏览器')
      })
    }
  }
}
</script>

<style scoped>
  .camera-container {
    position: absolute;
    min-height: 100vh;
    min-width: 100vw;
    overflow: hidden;
    z-index:100;
    animation: fadeIn .4s forwards;
    background-color: rgba(255,255,255,0.8);
  }
  @keyframes fadeOut {

    100% {
      transform: translateY(0);
    }
    0% {
      transform: translateY(100%);
    }

  }

  @keyframes fadeIn {
    0% {
      transform: translateY(100%);
    }
    100% {
      transform: translateY(0);
    }
  }

  .svg {
    position: absolute;
    z-index: 100;
    width: 100vw;
    height: 100vh;
  }
  .camera-actions {
    position: relative;
    width: 100vw;
    display: flex;
    flex-direction: column;
    flex-shrink: 1;
  }

  .print-screen {
    z-index: 121;
    display: inline-block;
    line-height: 1;
    white-space: nowrap;
    cursor: pointer;
    background: #fff;
    border: 1px solid #409eff;
    color: #409eff;
    -webkit-appearance: none;
    text-align: center;
    box-sizing: border-box;
    outline: none;
    transition: .1s;
    font-weight: 500;
    -moz-user-select: none;
    -webkit-user-select: none;
    -ms-user-select: none;
    padding: 12px 20px;
    font-size: 14px;
    border-radius: 4px;
  }

  .print-screen:active {
    cursor: pointer;
  }
  .active{
    background: #409eff;
    color: white;
  }

  /* 横屏时 */
  @media screen and (orientation: landscape) {
    .camera-container #video {
      position: absolute;
      width: 100vw;
      top: 50%;
      transform: translateY(-50%);
      overflow: hidden;
    }
    .camera-container #cav {
      position: absolute;
      z-index: 200;
    }
    .back-img {

    }

    .mask-rect,.camera-container #cav {
      transform: translate(15vw);
      width: 70vw;
      height: 100vh;
    }

    .camera-actions{
      margin-top: 45vh;
    }
    .print-screen{
      width: 5vw;
      margin-right: 10vw;
      margin-left: 90vw;
    }
  }

  @media screen and (orientation: portrait) {
    .camera-container #video {
      position: absolute;
      height: 100vh;
      left: 50%;
      transform: translateX(-50%);
    }

    .print-screen {
      width: 40vw;
      left: 0;
      right: 0;
      margin: 1vh auto;
    }

    .camera-actions{
      margin-top: 50vh;
    }

    .camera-container #cav {
      position: absolute;
      z-index: 200;
    }

    .mask-rect,.camera-container #cav {
      transform: translate(10vw,15vh);
      width: 80vw;
      height: 30vh;
    }
  }
</style>
