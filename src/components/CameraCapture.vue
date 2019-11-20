<template>
  <div class="camera-container" ref="container">
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
        <template v-if="!shot">
          <svg t="1574157718094" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
               p-id="1749" width="16" height="16">
            <path
              d="M889.10848 326.67136c31.41632 0.68608 73.26208 14.85312 73.26208 52.08064V863.83104c0 19.34336 2.23744 37.86752-11.99616 53.5296-12.032 13.23008-29.06624 11.99616-45.03552 11.99616H120.69888c-12.04224 0-23.6032 0.89088-34.87744-4.54144-22.48704-10.8288-22.15424-33.56672-22.15424-54.43072v-184.9344-228.096c0-37.41696-14.99648-119.0912 40.96-121.91744 32.85504-1.65888 33.01376-52.86912 0-51.2-50.46272 2.54976-92.16 40.54528-92.16 92.75904v479.3856c0 15.0784-1.024 30.6688 1.25952 45.62944 6.89664 45.19424 46.24384 78.55104 91.95008 78.55104h768.68096c49.66912 0 97.54624 2.08384 127.67744-47.5136 12.04224-19.80928 11.53024-42.58304 11.53024-64.73728v-80.82432-406.92736c0-67.74784-62.76608-103.73632-124.46208-105.07776-33.02912-0.73216-32.95744 50.47296 0.00512 51.18976z"
              p-id="1750" fill="#ffffff"></path>
            <path
              d="M183.88992 326.67136h33.16736c10.68032 0 22.58944-7.7312 24.68352-18.79552l16.39936-86.47168c5.78048-30.464 7.64928-65.29024 24.38144-92.10368 15.9488-25.55904 41.4976-22.22592 66.86208-22.22592h316.18048c74.96704 0 74.20928 99.26656 84.5056 153.5488l8.96512 47.25248c2.0992 11.06432 14.0032 18.79552 24.68352 18.79552h99.13344c33.01376 0 33.01376-51.2 0-51.2H783.7184l24.68352 18.79552-16.40448-86.4768c-5.94432-31.34464-8.94464-63.90784-23.41888-92.69248-35.74272-71.06048-114.95424-59.22816-180.37248-59.22816H355.69152c-21.64736 0-43.96032-1.792-64.88064 4.72576-41.84576 13.03552-64.68608 55.68512-72.31488 95.90784l-26.12736 137.7536 24.68352-18.79552h-33.16736c-33.01376 0.01024-33.01376 51.21024 0.00512 51.21024zM691.11808 582.9888c-1.66912 94.79168-76.7232 169.85088-171.52 171.52-94.82752 1.66912-169.92768-79.232-171.55072-171.52-1.66912-94.81216 79.24736-169.91744 171.55072-171.54048 94.80704-1.664 169.89696 79.2576 171.52 171.54048 0.57856 32.97792 51.77856 33.02912 51.2 0-2.07872-118.21056-91.55072-211.91168-208.64512-222.24384-115.49696-10.1888-220.88192 81.31584-234.86976 194.3552-14.32576 115.75808 64.98304 224.30208 179.28704 246.65088 115.10272 22.5024 227.39456-52.2496 257.42336-163.93216 4.76672-17.73056 6.48704-36.54144 6.80448-54.83008 0.58368-33.03936-50.61632-32.97792-51.2 0z"
              p-id="1751" fill="#ffffff"></path>
          </svg>
        </template>
        <template v-else>
          <svg t="1574213813913" class="icon" viewBox="0 0 1024 1024" version="1.1" xmlns="http://www.w3.org/2000/svg"
               p-id="2971" width="16" height="16">
            <path
              d="M1000.7956 359.920402c-17.526241-65.903606-61.761093-130.706857-63.702896-133.534239-9.20591-13.392554-27.523582-16.773056-40.913194-7.564204-12.474611 8.58218-16.2523 25.066908-9.26181 38.106407l-0.114743 0.050017c0.535467 0.788489 1.047397 1.59169 1.579921 2.383121 0.079437 0.123569 0.147107 0.253022 0.232428 0.376592 0.020595 0.035306 0.179469 0.264791 0.438377 0.653152 1.188618 1.779985 2.359585 3.577624 3.521724 5.378205 3.321659 5.172257 8.440957 13.395497 14.289903 23.681166 0.764952 1.359261 1.541674 2.706755 2.291915 4.074843 12.356926 22.186566 27.144048 52.446325 34.890664 81.958784 1.132718 3.61293 2.230131 7.24057 3.280469 10.891748 2.286031 7.976102 4.401419 16.03164 6.269669 24.184269 27.099915 117.946859 6.637435 239.391907-57.609755 341.957336-64.244248 102.565428-164.582487 173.970809-282.538174 201.06484-117.952744 27.099915-239.394849 6.637435-341.960277-57.606813s-173.970809-164.585429-201.06484-282.541116c-27.094031-117.949802-6.634492-239.394849 57.609755-341.957336 64.24719-102.565428 164.585429-173.973751 282.538174-201.06484 69.122291-15.87865 139.439085-15.399084 206.457757 0.623731 44.276043 10.591651 87.107502 27.964901 127.202786 51.925568 2.774424 1.6623 5.545906 3.33637 8.296793 5.060456l0.079437-0.126512c13.595561 7.61422 30.909968 3.321659 39.271488-10.026763 8.626311-13.772088 4.45732-31.927943-9.314768-40.551313-0.597251-0.37365-3.901258-2.427253-9.303-5.557675C535.015981-69.782878 226.08106 4.111541 78.171597 240.234746c-150.089579 239.60374-77.522059 555.51444 162.084623 705.604018 239.606682 150.089579 555.51444 77.519117 705.604018-162.087565C1028.316241 652.11463 1043.532912 497.461816 1000.7956 359.920402zM774.872743 316.268089l-262.187493 339.691899-6.39618 8.285025-8.429189-6.184347-221.071292-162.184655c-13.0954-9.611923-31.513103-6.78454-41.125027 6.316743-9.614865 13.104226-6.78454 31.513103 6.316743 41.122085l240.671731 176.568706 11.577263 8.493915c4.954539 2.356643 10.473966 3.712962 16.322911 3.712962 9.491297 0 18.152913-3.48936 24.825653-9.229447l9.391264-12.171572 276.683344-358.468542c9.929673-12.862972 7.549494-31.339518-5.313478-41.266249C803.276021 301.033765 784.802417 303.40806 774.872743 316.268089z"
              p-id="2972" fill="#ffffff"></path>
          </svg>
        </template>
      </button>

      <button v-show="shot" @click="cancelShot" class="print-screen">
        取消
      </button>
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
    this.mediaStreamTrack && this.mediaStreamTrack.stop()
    window.removeEventListener('resize', this.renderResize, false)
  },
  mounted () {
    this.hideCapture()
  },
  data: () => ({
    show: true,
    shot: false,
    status: 'portrait',
    stream: undefined
  }),
  methods: {
    // 判断横屏或者竖屏
    renderResize () {
      let width = document.documentElement.clientWidth
      let height = document.documentElement.clientHeight
      this.status = width > height ? 'landscape' : 'portrait'
    },
    // 关闭视频流
    stopStream () {
      if (this.stream) {
        this.stream.getTracks().forEach(track => {
          track.stop()
        })
      }
    },
    // 确定上传图片
    confirmShoot () {
      const canvas = this.$refs.cav
      if (!canvas) return
      const ctx = canvas.getContext('2d')

      if (this.shot) {
        this.stopStream()
        this.$refs.container.style.marginTop = '200vh'
        this.$refs.container.style.display = 'none'

        this.$emit('onConfirmShot', canvas.toDataURL())
        ctx.clearRect(0, 0, canvas.width, canvas.height)

        return
      }
      this.shot = true
      const video = document.querySelector('#video')
      if (!video) return

      const aspect = video.clientWidth / video.clientHeight
      // const cavAspect = canvas.clientHeight / canvas.clientWidth
      let x = 0
      let y = 0
      let dx = 0
      let dy = 0
      // 将video中的图像映射到canvas中
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
        dy = tmp * 0.75
      }

      ctx.drawImage(video, x, y, dx, dy, 0, 0, canvas.width, canvas.height)
    },
    // 返回按钮
    cancelShot () {
      this.shot = false
      const canvas = this.$refs.cav
      const ctx = canvas.getContext('2d')
      ctx.clearRect(0, 0, canvas.width, canvas.height)
    },
    // 显示并初始化整个弹窗
    showModal () {
      this.$refs.container.style.display = 'block'
      this.$refs.container.style.marginTop = '0'

      this.initCamera()

      const m = 2
      this.$refs.cav.width = this.$refs.cav.clientWidth * m
      this.$refs.cav.height = this.$refs.cav.clientHeight * m
      window.addEventListener('resize', this.renderResize, false)
    },
    // 隐藏弹窗
    hideCapture () {
      const canvas = this.$refs.cav
      const ctx = canvas.getContext('2d')
      ctx.clearRect(0, 0, canvas.width, canvas.height)
      this.stopStream()
      // this.$emit('onConfirmShot', undefined)
      this.$refs.container.style.marginTop = '200vh'
      this.$refs.container.style.display = 'none'
    },
    // 初始化相机
    async initCamera () {
      this.shot = false
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
        // video: { facingMode: 'user', width: width, height: height }// 或者 "user"
        // video: { width: 1280, height: 720 }
        video: { facingMode: 'environment', width: width, height: height }// 或者 "user"
      }
      navigator.mediaDevices.getUserMedia(mediaOpts).then(stream => {
        if ('srcObject' in video) {
          video.srcObject = stream
        } else {
          video.src = window.URL || window.URL.createObjectURL(stream) || stream
        }
        this.stream = stream
        video.play()
      }).catch(err => {
        alert(err.name + ': 您的浏览器不支持启用摄像头，请更换浏览器。')
      })
    }
  }
}
</script>

<style scoped>
  .camera-container {
    position: fixed;
    height: 100vh;
    width: 100vw;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    overflow: hidden;
    z-index: 10000;
    animation: fadeIn .4s forwards;
    transition: margin-top 0.8s;
    background: white;
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
    padding: 1vw 1.5vw;
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
      margin-bottom: 20vh;
      padding: 4vh 0;
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
      margin-top: 43vh;
    }

    .print-screen {
      width: 8vw;
      margin-left: 90vw;
      padding: 4vh 0;
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
