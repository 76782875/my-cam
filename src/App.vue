<template>
  <div id="app">
    <button style="position: absolute;margin-top: 50vh" @click="showCapture">拍摄</button>
    <input type="file" accept="image/*" >
    <camera-capture v-if="show" ref="capture" @hideCapture="hideCapture" />
  </div>
</template>

<script>

// import HelloWorld from './components/HelloWorld'
import CameraCapture from './components/CameraCapture'
export default {
  name: 'app',
  components: { CameraCapture },
  data: () => ({
    show: false
  }),
  methods: {
    showCapture () {
      this.show = true
    },
    hideCapture (val) {
      this.show = false
      console.log(val)
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
