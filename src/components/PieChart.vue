<template>
  <div class="chart-area">
    <div class="container">
      <div class="info">
        <div class="info__item">
          <div class="info__num">{{ serverData.data.total }}</div>
          <div class="info__title">{{ addEnding(serverData.data.total, empWords[0]) }} в штате</div>
        </div>
        <div class="info__item">
          <div class="info__num">{{ serverData.data.males }}</div>
          <div class="info__title">{{ addEnding(serverData.data.males, empWords[1]) }}</div>
        </div>
        <div class="info__item">
          <div class="info__num">{{ serverData.data.females }}</div>
          <div class="info__title">{{ addEnding(serverData.data.females, empWords[2]) }}</div>
        </div>
        <div class="info__item">
          <div class="info__num">{{ serverData.data.couples }}</div>
          <div class="info__title">{{ addEnding(serverData.data.couples, empWords[3]) }}</div>
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

export default {
  name: "PieChart",
  extends: Doughnut,
  data: () => ({
    serverData: require("@/test.json"),
    chartHiddenIndexes: {},
    empWords: ["Сотрудник", "Мужчин", "Женщин", "Пар"],
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
            var width = context.chart.width;
            var size = Math.round(width / 20);
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
    var legendItems = legendContainer.getElementsByTagName("li");
    for (var i = 0; i < legendItems.length; i += 1) {
      legendItems[i].addEventListener("click", this.legendClickCallback, false);
    }
  },
  methods: {
    legendClickCallback(event) {
      event = event || window.event;

      var target = event.target || event.srcElement;
      while (target.nodeName !== "LI") {
        target = target.parentElement;
      }
      var parent = target.parentElement;
      var chart = this.$data._chart;
      // console.log([...parent.children].indexOf(target));
      let index = Array.prototype.slice.call(parent.children).indexOf(target);
      var meta = chart.getDatasetMeta(0);
      var item = meta.data[index];
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
      var filteredData = data.filter(function (elem) {
        if (data.indexOf(elem) in hiddenIndexes) {
          return "";
        } else {
          return elem;
        }
      });
      const reducer = (accumulator, currentValue) => accumulator + currentValue;
      var total = filteredData.reduce(reducer);
      return total;
    },
    addEnding(num, word) {
      let lastNum = num % 10;
      if (word === "Сотрудник") {
        if (lastNum === 1 && num % 100 != 11) {
          return word;
        } else if (lastNum >= 2 && lastNum <= 4) {
          return word + "а";
        } else {
          return word + "ов";
        }
      } else {
        if (lastNum === 1 && num % 100 != 11) {
          return word + "а";
        } else if (lastNum >= 2 && lastNum <= 4) {
          return word + "ы";
        } else {
          return word;
        }
      }
    },
  },
};
</script>

<style lang="scss">
@import "../assets/chart.scss";
</style>
