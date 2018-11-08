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
    45deg,
    #f2bf30,
    #f2bf30 1rem,
    #000000 1rem,
    #000000 2rem
  );
  min-height: 100%;
}
#app {
  background: #eeeeee;
  border-radius: 0.5em; /* TODO: Border radius doesn't work? */
  color: #2c3e50;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 3rem;
  text-align: center;
}
</style>
