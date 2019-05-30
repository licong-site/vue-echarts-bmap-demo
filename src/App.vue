<template>
  <div id="app">
    <img class="logo" alt="Vue logo" src="./assets/logo.png">
    <Map />
  </div>
</template>

<script>
import Map from './components/Map.vue'

export default {
  name: 'app',
  components: {
    Map
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  height: 100vh;
  overflow: hidden;
}
.logo {
  position: absolute;
  top: 50px;
  right: 50px;
  z-index: 10;
  width: 80px;
  height: 80px;
}
</style>
