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
                <b-button size="sm" @click="submitData(row.item)" class="mr-1">Submit</b-button>
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
            <button @click="submitDataSocket()">Send To Socket IO</button>
          </b-card>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import ChartLine from "./ChartLine.js";
import axios from 'axios'

export default {
  components: {
    ChartLine
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
      selected: "Dickerson",
      optionsRadio: [
        { text: 'Total Data di Dickerson', value: 'Dickerson' },
        { text: 'Total Data di Larsen', value: 'Larsen' },
        { text: 'Total Data di Geneva', value: 'Geneva' },
        { text: 'Total Data di Jami', value: 'Jami' },
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
      fields: ["data_id", "first_name", "actions"],
      mainData: {
        Dickerson: {
          labelChart: 'Data di Dickerson',
          indexData: 0,
          colorChart: 'rgb(101, 189, 101)',
          chartData: [21, 10, 10, 30, 15, 40, 20, 20],
        },
        Larsen: {
          labelChart: 'Data di Larsen',
          indexData: 0,
          colorChart: 'rgb(191,169,89)',
          chartData: [1, 15, 10, 30, 15, 40, 20, 20],
        },
        Geneva: {
          labelChart: 'Data di Geneva',
          indexData: 0,
          colorChart: 'rgb(202,79,116)',
          chartData: [15, 10, 10, 30, 15, 40, 20, 20],
        },
        Jami: {
          labelChart: 'Data di Jami',
          indexData: 0,
          colorChart: 'rgb(44,184,227)',
          chartData: [40, 20, 10, 30, 15, 40, 20, 20],
        },
      },
    };
  },
  mounted() {
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
      this.bigLineChart.allData[indexData][0] = newTotal;
      this.initBarChart();
    },
    async initBarChart() {
      await this.selected;
      const resultMappingName = this.mainData[this.selected];
      const chartData = {
        datasets: [
          {
            label: resultMappingName.labelChart,
            data: resultMappingName.chartData,
            borderColor: resultMappingName.colorChart,
          },
        ],
        labels: ['Jan', 'Feb', 'Mar', 'April', 'May', 'Jun', 'Jul', 'Aug'],
      };
      this.bigLineChart.chartData = chartData;
      this.bigLineChart.activeIndex = resultMappingName.indexData;
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
    },
    async submitData(item) {
      const data = {
        owner: item.first_name,
        id: item.data_id,
      };
      await axios.post(
        'http://localhost:3000/ground/applications',
        data,
      );
    },
  },
  sockets: {
    updateApplication(data) {
      const resultMappingName = this.mainData[data.owner];
      const chartData = {
        datasets: [
          {
            label: resultMappingName.labelChart,
            data: data.dataChart,
            borderColor: resultMappingName.colorChart,
          },
        ],
        labels: ['Jan', 'Feb', 'Mar', 'April', 'May', 'Jun', 'Jul', 'Aug'],
      };
      this.bigLineChart.chartData = chartData;
      this.bigLineChart.activeIndex = resultMappingName.indexData;
    },
    connected(data) {
      console.log(data);
    },
  },
  created() {
    this.$options.sockets.vote = (data) => {
      console.log('hasil created', data);
    };
  }
};
</script>

<style scoped>
.height-chart {
  max-height: 250px;
}
</style>