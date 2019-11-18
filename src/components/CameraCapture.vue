<template>
  <div class="camera-container">
    <img src="@/assets/cancel.svg" alt="取消" @click="hideCapture" style="position: absolute;z-index: 109;margin-left: 5vw;margin-top: 2vh;border: 2px solid #dddddd;border-radius: 5px;padding: 6px" />
    <svg class="svg">
      <defs>
        <mask id="myMask">
          <rect x="0" y="0" width="100%" height="100%" style="fill: #fff"></rect>
          <rect ref="maskRect" class="mask-rect"></rect>
        </mask>
      </defs>
      <rect x="0" y="0" width="100vw" height="100vh"
            style="stroke: none; fill: rgba(0, 0, 0, .8); mask: url(#myMask);"></rect>
    </svg>
    <video id="video" ref="video" autoplay playsinline></video>
    <canvas id="cav" ref="cav"></canvas>

    <div class="camera-actions">
      <button class="print-screen active print1" @click="confirmShoot">{{ shot ? '确认并上传' : '点击拍摄' }}</button>
      <button v-show="shot" @click="cancelShot" class="print-screen">取消截图</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CameraCapture',
  props: {
    mode: {
      type: [Boolean, String],
      default: 'user'
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
      if (this.shot) {
        this.$emit('hideCapture', 'value')
        return
      }
      this.shot = true
      const canvas = this.$refs.cav
      const video = document.querySelector('video')

      const ctx = canvas.getContext('2d')
      const aspect = window.innerWidth / window.innerHeight
      const tmpWidth = video.videoHeight * aspect
      const x = (video.videoWidth - tmpWidth) / 2 + tmpWidth * 0.2
      const y = video.videoHeight * 0.25
      ctx.drawImage(video, x, y, tmpWidth * 0.6, tmpWidth * 0.6, 0, 0, canvas.width, canvas.height)
      console.log(canvas.toDataURL())
    },
    cancelShot () {
      this.shot = false
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
        alert(err)
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
    animation: fadeIn .4s forwards;
  }

  @keyframes fadeIn {
    0% {
      transform: translateY(100%);
    }
    100% {
      transform: translateY(0);
    }
  }
  .camera-container #cav {
    position: absolute;
    z-index: 1000;
    width: 100%;
    height: 100%;
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
    z-index: 120;
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

    .mask-rect {
      transform: translate(15%, 15%);
      width: 70%;
      height: 70%;
    }

    .camera-actions{
      top: 0;
      bottom: 0;
    }
    .print-screen{
      width: 5vw;
      margin-right: 10vw;
      margin-left: 90vw;
    }
    .print1{
      margin-top: 45vh;
    }
  }

  @media screen and (orientation: portrait) {
    .camera-container #video {
      position: relative;
      height: 100vh;
      /*height: 100vh;*/
      /*left: 50%;*/
      /*transform: translateX(-50%);*/
    }

    .print-screen {
      width: 40vw;
      left: 0;
      right: 0;
      margin: 1vh auto;
    }

    .mask-rect {
      transform: translate(10vw,15vh);
      width: 80vw;
      height: 30vh;
    }
  }
</style>
