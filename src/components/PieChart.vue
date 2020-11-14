<template>
  <div class="pie-chart">
    <canvas ref="canvas" id="departmentChart"></canvas>
    <div id="legend"></div>
  </div>
</template>

<script>
import { Doughnut } from "vue-chartjs";
import "chartjs-plugin-labels";

export default {
  name: "PieChart",
  extends: Doughnut,
  // props: {
  //   msg: String
  // }
  // data: () => ({
  //   htmlLegend: null
  // }),
  mounted() {
    const data = {
      datasets: [
        {
          data: [3, 54, 13, 22],
          backgroundColor: ["#2B78FE", "#8295FA", "#FFBB49", "#87D227"],
        },
      ],

      labels: [
        "Администрация",
        "Отдел обеспечения магического правопорядка",
        "Отдел магических происшествий и катастроф",
        "Отдел регулирования магических популяций",
      ],
    };
    const options = {
      plugins: {
        labels: {
          render: "percentage",
          fontSize: 30,
          fontFamily: "Helvetica Neue",
          fontStyle: "normal",
          fontColor: "#fff",
          precision: 1,
          overlap: false,
        },
      },
      legend: false,
      legendCallback: function (chart) {
        var list = document.createElement("ul");
        var data = chart.data;
        var datasets = data.datasets;
        var labels = data.labels;
        var i, ilen, listItem, listItemSpan;
        list.setAttribute("class", chart.id + "-legend");
        if (datasets.length) {
          for (i = 0, ilen = datasets[0].data.length; i < ilen; ++i) {
            listItem = list.appendChild(document.createElement("li"));
            listItemSpan = listItem.appendChild(document.createElement("span"));
            listItemSpan.style.backgroundColor = datasets[0].backgroundColor[i];
            if (labels[i]) {
              listItem.appendChild(document.createTextNode(labels[i]));
            }
          }
        }

        return list.outerHTML;
      },
    };

    this.renderChart(data, options);
    let legendContainer = document.getElementById("legend");
    legendContainer.innerHTML = this.generateLegend();
    var legendItems = legendContainer.getElementsByTagName("li");
    for (var i = 0; i < legendItems.length; i += 1) {
      legendItems[i].addEventListener("click", this.legendClickCallback, false);
    }
    // console.log(this.$data._chart);
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
      var index = Array.prototype.slice.call(parent.children).indexOf(target);
      var meta = chart.getDatasetMeta(0);
      var item = meta.data[index];

      if (item.hidden === null || item.hidden === false) {
        item.hidden = true;
        target.classList.add("hidden");
      } else {
        target.classList.remove("hidden");
        item.hidden = null;
      }
      chart.update();
    },
  },
};
</script>

<style lang="scss">
@import "../assets/chart.scss";

[class$="-legend"] {
  list-style: none;
  cursor: pointer;
  padding-left: 0;
}

[class$="-legend"] li {
  display: inline-block;
  padding: 0 5px;
}

[class$="-legend"] li.hidden {
  text-decoration: line-through;
}

[class$="-legend"] li span {
  border-radius: 5px;
  display: inline-block;
  height: 10px;
  margin-right: 10px;
  width: 10px;
}
</style>
