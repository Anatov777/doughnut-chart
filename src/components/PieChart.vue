<template>
  <div class="chart-area">
    <div class="container">
      <div class="info">
        <div class="info__item" v-for="(item, index) in infoItems" :key="'chart-info-' + index">
          <div class="info__num">{{ item[0] }}</div>
          <div class="info__title">{{ item[1] }}</div>
        </div>
      </div>
      <h2>По департаментам</h2>
      <div class="doughnut-chart">
        <canvas
          ref="canvas"
          id="departmentChart"
          width="100%"
          height="100%"
        ></canvas>
        <div class="legend" id="legend"></div>
      </div>
    </div>
  </div>
</template>

<script>
import { Doughnut } from "vue-chartjs";
import "chartjs-plugin-datalabels";

const empWords = [
  ['Сотрудник', 'Сотрудника', 'Сотрудников'],
  ['Мужчина', 'Мужчины', 'Мужчин'],
  ['Женщина', 'Женщины', 'Женщин'],
  ['Пара', 'Пары', 'Пар']
]

export default {
  name: "PieChart",
  extends: Doughnut,
  data: () => ({
    serverData: require("@/test.json"),
    chartHiddenIndexes: {},
  }),
  mounted() {
    let self = this;
    const data = {
      datasets: [
        {
          data: this.serverData.data.departments.map((emp) => emp.employees),
          backgroundColor: [
            "#2B78FE",
            "#8295FA",
            "#FFBB49",
            "#87D227",
            "#20CBAC",
            "#01B0D7",
            "#6235B0",
            "#9559FF",
            "#C055D1",
            "#FF7979",
          ],
        },
      ],
      labels: this.serverData.data.departments.map((name) => name.title),
    };
    const options = {
      cutoutPercentage: 56,
      legend: false,
      elements: {
        arc: {
          borderWidth: 0,
        },
      },
      tooltips: {
        bodyFontSize: 30,
      },
      plugins: {
        datalabels: {
          display: "auto",
          color: "#fff",
          font: function (context) {
            let width = context.chart.width;
            let size = Math.round(width / 20);
            return {
              size: size,
              family: "Roboto",
              weight: 500,
            };
          },
          formatter: function (value, context) {
            if (+((value * 100) / self.total(context)).toFixed(1) > 5) {
              return ((value * 100) / self.total(context)).toFixed(1) + "%";
            } else {
              return "";
            }
          },
        },
      },
    };
    this.renderChart(data, options);
    let legendContainer = document.getElementById("legend");
    legendContainer.innerHTML = this.generateLegend();
    let legendItems = legendContainer.getElementsByTagName("li");
    for (let i = 0; i < legendItems.length; i += 1) {
      legendItems[i].addEventListener("click", this.legendClickCallback, false);
    }
  },
  computed: {
    infoItems() {
      const info = [
        [this.serverData.data.total, this.getDeclension(this.serverData.data.total, empWords[0]) + ' в штате'],
        [this.serverData.data.males, this.getDeclension(this.serverData.data.males, empWords[1])],
        [this.serverData.data.females, this.getDeclension(this.serverData.data.females, empWords[2])],
        [this.serverData.data.couples, this.getDeclension(this.serverData.data.couples, empWords[3])]
      ]
      return info
    },
  },
  methods: {
    legendClickCallback(event) {
      event = event || window.event;

      let target = event.target || event.srcElement;
      while (target.nodeName !== "LI") {
        target = target.parentElement;
      }
      let parent = target.parentElement;
      let chart = this.$data._chart;
      let index = Array.prototype.slice.call(parent.children).indexOf(target);
      let meta = chart.getDatasetMeta(0);
      let item = meta.data[index];
      if (item.hidden === null || item.hidden === false) {
        item.hidden = true;
        target.classList.add("hidden");
        this.chartHiddenIndexes[index] = index;
      } else {
        target.classList.remove("hidden");
        item.hidden = null;
        delete this.chartHiddenIndexes[index];
      }
      chart.update();
    },
    total(chart) {
      let data = chart.chart.data.datasets[0].data;
      let hiddenIndexes = this.chartHiddenIndexes;
      let filteredData = data.filter(function (elem) {
        if (data.indexOf(elem) in hiddenIndexes) {
          return "";
        } else {
          return elem;
        }
      });
      const reducer = (accumulator, currentValue) => accumulator + currentValue;
      let total = filteredData.reduce(reducer);
      return total;
    },
    getDeclension(number, txt) {
      let cases = [2, 0, 1, 1, 1, 2]
      return txt[number % 100 > 4 && number % 100 < 20 ? 2 : cases[number % 10 < 5 ? number % 10 : 5]]
    }
  }
}
</script>
