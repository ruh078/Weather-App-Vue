<template>
  <Weather />
</template>

<script>
import Weather from './components/Weather.vue'

export default {
  name: 'App',
  components: {
    Weather
  }
}
</script>

<style>
#app {
  text-align: center;
  min-height: 100vh;
}
</style>
