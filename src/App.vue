<template>
  <div id="app">
    <FileImports />
    <AudioBoard />
  </div>
</template>

<script>
import AudioBoard from './components/AudioBoard.vue'
import FileImports from './components/FileImports.vue'


export default {
  name: 'app',
  components: {
    FileImports,
      AudioBoard
  }
}
</script>

<style>

  *{
    margin: 0;
    padding: 0;
  }

  html{
    font-size: 10px;
  }

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  display: flex;
  overflow: hidden;
}
</style>
