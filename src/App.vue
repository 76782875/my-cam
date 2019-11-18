<template>
  <div id="app">
    <button style="position: absolute;margin-top: 50vh" @click="showCapture">拍摄</button>
    <camera-capture v-if="show" ref="capture" @hideCapture="hideCapture" />
    <img :src="imgSrc" alt="图片" style="position: absolute;top: 30vh" />
  </div>
</template>

<script>

// import HelloWorld from './components/HelloWorld'
import CameraCapture from './components/CameraCapture'
export default {
  name: 'app',
  components: { CameraCapture },
  data: () => ({
    show: false,
    imgSrc: ''
  }),
  methods: {
    showCapture () {
      this.show = true
    },
    hideCapture (val) {
      this.show = false
      this.imgSrc = val
    }
  }

  // components: { HelloWorld }
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
</style>
