<template>
  <div id="app">
    <Estadisticas/>
  </div>
</template>

<script>
import Estadisticas from './components/Estadisticas.vue'

export default {
  name: 'App',
  components: {
    Estadisticas
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
