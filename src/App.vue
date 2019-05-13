<template>
  <div id="app">
    <div style="width : 30%">
      <ez-vue-uploader min-height-upload-span="100px" :max-files="5" :with-button-upload="false"></ez-vue-uploader>
    </div>
  </div>
</template>

<script>
import ezVueUploader from './components/ez-vue-uploader';
export default {
  name: 'app',
  components: {
    ezVueUploader
  }
}
</script>

<style lang="scss">
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
