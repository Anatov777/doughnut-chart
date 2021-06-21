<template>
  <div id="app">
    <header class="header">
      <div class="container">
        <h1>В настоящий момент в министерстве магии</h1>
      </div>
    </header>
    <PieChart />
    <footer class="footer">
      <div class="container">
        <div class="copyright">
          ООО “Нейрософт”, 2020<br />
          Медицинское оборудование
        </div>
        <div class="socials">
          <div class="socials__text">Подписывайся в соц сетях</div>
          <div class="socials__icons">
            <a href="#"><img src="images/inst.svg" alt="Instagram" /></a>
            <a href="#"><img src="images/vk.svg" alt="VK" /></a>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>

<script>
import PieChart from "./components/PieChart.vue";
import '@/assets/chart.scss'

export default {
  name: "App",
  components: {
    PieChart,
  },
};
</script>

<style lang="scss">
@import "./assets/style.scss";
@import "./assets/media.scss";
</style>
