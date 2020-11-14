<template>
  <div id="app">
    <PieChart />
  </div>
</template>

<script>
import PieChart from './components/PieChart.vue'

export default {
  name: 'App',
  components: {
    PieChart
  }
}
</script>

<style lang="scss">
  @import "./assets/style.scss";
</style>
