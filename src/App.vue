<template>
  <div id="app">
<!--    <button @click="clickBtn">点我啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊啊</button>-->
<!--    <input type="file" ref="inputa" accept="image/*" capture="camera" hidden />-->
    <button style="position: absolute;margin: auto;width: 100%;display: block;bottom: 20%" @click="showCapture">拍摄</button>
    <camera-capture v-if="show" ref="capture" @hideCapture="hideCapture" />
    <img :src="imgSrc" alt="图片" style="position: absolute;width: 100%;display: block;top: 30%;" />
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
    clickBtn () {
      console.log('clicked')
      this.$refs.inputa.click()
    },
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
