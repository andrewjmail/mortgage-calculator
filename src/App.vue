<template>
  <div id="app">
    <Options />
  </div>
</template>

<script>
import Options from './components/Options.vue'

export default {
  name: 'app',
  components: {
    Options
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
