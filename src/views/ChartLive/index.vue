<template>
  <div>
    <base-header type="gradient-success" class="pb-6 pb-8 pt-5 pt-md-8"></base-header>

    <div class="container-fluid mt--7">
      <div class="row">
        <div class="col">
          <!-- for table list -->
          <b-card class="mb-4">
            <b-table striped hover :items="items" :fields="fields">
              <template v-slot:actions="row">
                <b-button size="sm" @click="info(row.item)" class="mr-1">Submit</b-button>
              </template>
            </b-table>
          </b-card>

          <b-card>
            <b-form-group label>
              <b-form-radio-group
                id="btn-radios-2"
                v-model="selected"
                :options="optionsRadio"
                buttons
                button-variant="outline-primary"
                size="lg"
                name="radio-btn-outline"
                @change="initBarChart()"
              ></b-form-radio-group>
            </b-form-group>
            {{selected}}
            <ChartLine :height="400" :options="options" :chartData="bigLineChart.chartData"></ChartLine>
            <!-- <button @click="fillData()">Randomize</button> -->
          </b-card>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ChartLine from "./ChartLine.js";
export default {
  //   name: "live-chart",
  components: {
    ChartLine
    // List
  },
  data() {
    return {
      bigLineChart: {
        allData: [
          [0, 20, 10, 30, 15, 40, 20, 20],
          [25, 20, 5, 25, 10, 30, 15, 40]
        ],
        activeIndex: 0,
        chartData: {
          datasets: [],
          labels: []
        }
      },
      selected: "RM",
      optionsRadio: [
        { text: "Total Data Pending di RM", value: "RM" },
        { text: "Total Data Pending di ACM", value: "ACM" }
      ],
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          xAxes: [
            {
              gridLines: {
                display: false
              }
            }
          ],
          yAxes: [
            {
              gridLines: {
                display: true
              }
            }
          ]
        }
      },
      datacollection: null,
      items: [
        { data_id: 1, first_name: "Dickerson" },
        { data_id: 2, first_name: "Larsen" },
        { data_id: 3, first_name: "Geneva" },
        { data_id: 4, first_name: "Jami" }
      ],
      fields: ["data_id", "first_name", "actions"]
    };
  },
  mounted() {
    // this.fillData();
    this.initBarChart();
  },
  methods: {
    info(item) {
      let indexData;
      if (this.selected === "RM") {
        indexData = 0;
      } else {
        indexData = 1;
      }
      let newTotal = this.bigLineChart.allData[indexData][0] + 10;
      console.log(newTotal);
      this.bigLineChart.allData[indexData][0] = newTotal;
      console.log(this.bigLineChart.allData[indexData])
      this.initBarChart();
    },
    async initBarChart() {
      await this.selected;
      console.log(this.selected);
      let labelChart = "";
      let indexData;
      let colorChart;
      if (this.selected === "RM") {
        labelChart = "Data di RM";
        indexData = 0;
        colorChart = "rgb(101, 189, 101)";
      } else {
        labelChart = "Data di ACM";
        indexData = 1;
        colorChart = "rgb(49, 49, 199)";
      }
      let chartData = {
        datasets: [
          {
            label: labelChart,
            data: this.bigLineChart.allData[indexData],
            borderColor: colorChart
          }
        ],
        labels: ["Jan", "Feb", "Mar", "April", "May", "Jun", "Jul", "Aug"]
      };
      this.bigLineChart.chartData = chartData;
      this.bigLineChart.activeIndex = indexData;
    },
    fillData() {
      this.datacollection = {
        // labels: [this.getRandomInt(), this.getRandomInt()],
        labels: ["Jan", "Feb", "Mar", "April", "May"],
        datasets: [
          {
            label: "Data One",
            // backgroundColor: "#f87979",
            borderColor: "rgb(101, 189, 101)",
            data: [3, 2, 5, 3.5, 4]
          },
          {
            label: "Data Two",
            // backgroundColor: "grey",
            borderColor: "rgb(155, 83, 83)",
            data: [2, 5, 3, 1, 3]
          }
          //   {
          //     label: "Data Two",
          //     backgroundColor: "#f87979",
          //     data: [this.getRandomInt(), this.getRandomInt()]
          //   }
        ]
      };
    },
    getRandomInt() {
      return Math.floor(Math.random() * (10 - 5 + 1)) + 2;
    }
  }
};
</script>

<style scoped>
.height-chart {
  max-height: 250px;
}
</style>