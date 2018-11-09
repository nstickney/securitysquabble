<template>
  <div id="app">
    <SecuritySquabble msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import SecuritySquabble from './components/SecuritySquabble.vue'

export default {
  name: 'app',
  components: {
    SecuritySquabble
  }
}
</script>

<style>
html {
  min-height: 100%;
}
body {
  background: repeating-linear-gradient(
    135deg,
    #f2bf30,
    #f2bf30 2rem,
    #000000 2rem,
    #000000 4rem
  );
  min-height: 100%;
  padding: 1rem;
}
#app {
  background: #eeeeee;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 0 auto;
  max-width: 80rem;
  text-align: center;
}
</style>

// vim: ai ts=2 sts=2 et sw=2
