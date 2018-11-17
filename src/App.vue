<template>
  <SecuritySquabble />
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
  background: #555;
  font-family: -apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,Oxygen-Sans,Ubuntu,Cantarell,"Helvetica Neue",sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  margin: 0;
  min-height: 100%;
  padding: 0;
}
@media only screen and (min-width: 768px) {
  body {
    padding: 1rem;
  }
}
</style>

// vim: ai ts=2 sts=2 et sw=2
