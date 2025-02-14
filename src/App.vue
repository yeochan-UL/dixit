<template>
  <DixitScoreboard />
</template>

<script>
import DixitScoreboard from './components/DixitScoreboard.vue'

export default {
  name: 'App',
  components: {
    DixitScoreboard
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
