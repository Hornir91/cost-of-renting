<template>
  <div id="app">
    <EquipmentCalculator msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
import EquipmentCalculator from './components/EquipmentCalculator.vue'

export default {
  name: 'App',
  components: {
    EquipmentCalculator
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
