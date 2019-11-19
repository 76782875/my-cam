<template>
  <div class="camera-container">
    <a class="back-img" @click="hideCapture">
      返回
    </a>
    <svg class="svg">
      <defs>
        <mask id="myMask">
          <rect x="0" y="0" width="100%" height="100%" style="fill: #fff"></rect>
          <rect ref="maskRect" rx="10" ry="10" class="mask-rect"></rect>
        </mask>
      </defs>
      <rect class="mask-rect" rx="10" ry="10" style="fill: none;stroke: #409eff;stroke-width: 2"></rect>
      <rect x="0" y="0" width="100vw" height="100vh"
            style="stroke: none; fill: rgba(255, 255, 255, 1); mask: url(#myMask);"></rect>
    </svg>
    <video id="video" ref="video" autoplay playsinline></video>
    <canvas id="cav" ref="cav"></canvas>

    <div class="camera-actions" v-if="status === 'portrait'">
      <button class="print-screen active print1" @click="confirmShoot">
        <span>{{ shot ? '确认并上传' : '点击拍摄'}}</span></button>
      <button v-show="shot" @click="cancelShot" class="print-screen">取消截图</button>
    </div>
    <div class="camera-actions" v-if="status === 'landscape'">
      <button class="print-screen active print1" @click="confirmShoot">
        <svg t="1574157718094" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg" p-id="1749" width="16" height="16"><path d="M889.10848 326.67136c31.41632 0.68608 73.26208 14.85312 73.26208 52.08064V863.83104c0 19.34336 2.23744 37.86752-11.99616 53.5296-12.032 13.23008-29.06624 11.99616-45.03552 11.99616H120.69888c-12.04224 0-23.6032 0.89088-34.87744-4.54144-22.48704-10.8288-22.15424-33.56672-22.15424-54.43072v-184.9344-228.096c0-37.41696-14.99648-119.0912 40.96-121.91744 32.85504-1.65888 33.01376-52.86912 0-51.2-50.46272 2.54976-92.16 40.54528-92.16 92.75904v479.3856c0 15.0784-1.024 30.6688 1.25952 45.62944 6.89664 45.19424 46.24384 78.55104 91.95008 78.55104h768.68096c49.66912 0 97.54624 2.08384 127.67744-47.5136 12.04224-19.80928 11.53024-42.58304 11.53024-64.73728v-80.82432-406.92736c0-67.74784-62.76608-103.73632-124.46208-105.07776-33.02912-0.73216-32.95744 50.47296 0.00512 51.18976z" p-id="1750" fill="#ffffff"></path><path d="M183.88992 326.67136h33.16736c10.68032 0 22.58944-7.7312 24.68352-18.79552l16.39936-86.47168c5.78048-30.464 7.64928-65.29024 24.38144-92.10368 15.9488-25.55904 41.4976-22.22592 66.86208-22.22592h316.18048c74.96704 0 74.20928 99.26656 84.5056 153.5488l8.96512 47.25248c2.0992 11.06432 14.0032 18.79552 24.68352 18.79552h99.13344c33.01376 0 33.01376-51.2 0-51.2H783.7184l24.68352 18.79552-16.40448-86.4768c-5.94432-31.34464-8.94464-63.90784-23.41888-92.69248-35.74272-71.06048-114.95424-59.22816-180.37248-59.22816H355.69152c-21.64736 0-43.96032-1.792-64.88064 4.72576-41.84576 13.03552-64.68608 55.68512-72.31488 95.90784l-26.12736 137.7536 24.68352-18.79552h-33.16736c-33.01376 0.01024-33.01376 51.21024 0.00512 51.21024zM691.11808 582.9888c-1.66912 94.79168-76.7232 169.85088-171.52 171.52-94.82752 1.66912-169.92768-79.232-171.55072-171.52-1.66912-94.81216 79.24736-169.91744 171.55072-171.54048 94.80704-1.664 169.89696 79.2576 171.52 171.54048 0.57856 32.97792 51.77856 33.02912 51.2 0-2.07872-118.21056-91.55072-211.91168-208.64512-222.24384-115.49696-10.1888-220.88192 81.31584-234.86976 194.3552-14.32576 115.75808 64.98304 224.30208 179.28704 246.65088 115.10272 22.5024 227.39456-52.2496 257.42336-163.93216 4.76672-17.73056 6.48704-36.54144 6.80448-54.83008 0.58368-33.03936-50.61632-32.97792-51.2 0z" p-id="1751" fill="#ffffff"></path></svg>      </button>

      <button v-show="shot" @click="cancelShot" class="print-screen"></button>
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
  created () {
  },
  beforeDestroy () {
    window.removeEventListener('resize', this.renderResize, false)
  },
  mounted () {
    this.initCamera()
    const m = 12
    this.$refs.cav.width = this.$refs.cav.clientWidth * m
    this.$refs.cav.height = this.$refs.cav.clientHeight * m
    window.addEventListener('resize', this.renderResize, false)
  },
  data: () => ({
    show: false,
    shot: false,
    status: 'portrait'
  }),
  methods: {
    renderResize () {
      let width = document.documentElement.clientWidth
      let height = document.documentElement.clientHeight
      if (width > height) {
        this.status = 'landscape'
      } else {
        this.status = 'portrait'
      }
    },
    confirmShoot () {
      const canvas = this.$refs.cav

      if (this.shot) {
        this.$emit('hideCapture', canvas.toDataURL())
        return
      }
      this.shot = true
      const video = document.querySelector('#video')

      const ctx = canvas.getContext('2d')
      const aspect = video.clientWidth / video.clientHeight
      // const cavAspect = canvas.clientHeight / canvas.clientWidth
      let x = 0
      let y = 0
      let dx = 0
      let dy = 0

      if (window.innerWidth < window.innerHeight) {
        let tmpWidth = video.videoHeight * aspect
        x = (video.videoWidth - tmpWidth) / 2 + tmpWidth * 0.1
        y = video.videoHeight * 0.15
        dy = video.videoHeight * 0.3
        dx = dy * canvas.width / canvas.height
        // dy = dx * cavAspect
      } else {
        let tmp = video.videoWidth / aspect
        x = video.videoWidth * 0.15
        y = (video.videoHeight - tmp) / 2 + tmp * 0.1
        dx = video.videoWidth * 0.7
        dy = tmp * 0.8
      }

      ctx.drawImage(video, x, y, dx, dy, 0, 0, canvas.width, canvas.height)
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

      const width = 1280
      const height = 720

      // 摄像头调用配置
      const mediaOpts = {
        audio: false,
        // video: { facingMode: 'user', width: width, height: width }// 或者 "user"
        // video: { width: 1280, height: 720 }
        video: { facingMode: { exact: 'environment' }, width: width, height: height }// 或者 "user"
      }
      navigator.mediaDevices.getUserMedia(mediaOpts).then(stream => {
        if ('srcObject' in video) {
          video.srcObject = stream
        } else {
          video.src = window.URL || window.URL.createObjectURL(stream) || stream
        }
        video.play()
      }).catch(err => {
        alert(err.name + ': 您的浏览器不支持启用摄像头，请更换浏览器。' + err)
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
    z-index: 100;
    animation: fadeIn .4s forwards;
    background-color: rgba(255, 255, 255, 0.8);
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

  .active {
    background: #409eff;
    color: white;
  }

  .back-img {
    position: absolute;
    z-index: 109;
    margin-left: 10vw;
    margin-top: 5vh;
    border: 1px solid #409eff;
    color: #409eff;
    border-radius: 5px;
    padding: 6px
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

    .print1 {
      margin-bottom: 25vh;
      padding: 5vh 0;
    }

    .back-img {
      margin-top: 10vh;
      margin-left: 90vw;
    }

    .mask-rect, .camera-container #cav {
      transform: translate(15vw, 10vh);
      width: 70vw;
      height: 80vh;
    }

    .camera-actions {
      margin-top: 45vh;
    }

    .print-screen {
      width: 10vw;
      margin-left: 90vw;
    }
  }

  @media screen and (orientation: portrait) {
    .camera-container #video {
      position: absolute;
      padding: 0;
      margin: 0;
      width: 100vw;
      height: 100vh;
      left: 50%;
      top: 50%;
      transform: translate(-50%, -50%);
    }

    .print-screen {
      width: 40vw;
      left: 0;
      right: 0;
      margin: 1vh auto;
    }

    .camera-actions {
      margin-top: 50vh;
    }

    .camera-container #cav {
      position: absolute;
      z-index: 200;
    }

    .mask-rect, .camera-container #cav {
      transform: translate(10vw, 15vh);
      width: 80vw;
      height: 30vh;
    }
  }
</style>
