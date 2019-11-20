<template>
  <div id="app">
    <camera-capture ref="capture" @onConfirmShot="onShot"/>
    <img :src="imgSrc" alt="图片" style="width: 100%;display: block;"/>
    <button class="shot" @click="showCapture">拍摄</button>
  </div>
</template>

<script>

import CameraCapture from './components/CameraCapture'

export default {
  name: 'app',
  components: { CameraCapture },
  data: () => ({
    imgSrc: ''
  }),
  methods: {
    showCapture () {
      this.$refs.capture.showModal()
    },
    onShot (val) {
      this.imgSrc = val
      console.log(val)
    }
  }
}
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    margin: 0;
    padding: 0;
    width: 100%;
    height: 100%;
  }

  .shot {
    margin: auto;
    width: 100%;
    z-index: 121;
    display: inline-block;
    line-height: 1;
    margin-top: 1vh ;
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
</style>
