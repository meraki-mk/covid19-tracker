<template>
  <div>
    <h2>Covid-19 Tracker</h2>
    <div v-if="covidData">
      <!-- Widgets -->
      <WidgetComponent :title="totalCasesWidget.title" :value="totalCasesWidget.value" />
      <WidgetComponent :title="totalDeathsWidget.title" :value="totalDeathsWidget.value" />
      <WidgetChartComponent :title="chartWidget.title" :chartData="chartData" />

      <h2>Regional Data</h2>
      <ul>
        <li v-for="region in covidData.regional" :key="region.loc">
          <p>Location: {{ region.loc }}</p>
          <p>Total Cases: {{ region.totalConfirmed }}</p>
          <p>Recovered: {{ region.discharged }}</p>
          <p>Deaths: {{ region.deaths }}</p>
        </li>
      </ul>
    </div>
    <div v-else>
      <p>Loading...</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import WidgetComponent from "./WidgetComponent.vue";
import WidgetChartComponent from "./WidgetChartComponent.vue";

export default {
  components: {
    WidgetComponent,
    WidgetChartComponent,
  },
  data() {
    return {
      covidData: null,
      totalCasesWidget: {
        title: 'Total Cases',
        value: 0,
      },
      totalDeathsWidget: {
        title: 'Total Deaths',
        value: 0,
      },
      chartWidget: {
        title: 'Chart Widget',
      },
      chartData: {}, // Initialize an empty object to hold chart data
    };
  },
  created() {
    this.fetchCovidData();
  },
  methods: {
    async fetchCovidData() {
      try {
        const response = await axios.get('https://api.rootnet.in/covid19-in/stats/latest');
        this.covidData = response.data.data;

        // Update widget values
        this.totalCasesWidget.value = this.covidData.summary.total;
        this.totalDeathsWidget.value = this.covidData.summary.deaths;

        // Calculate chart data
        this.chartData = {
          cases: this.covidData['unofficial-summary'].map(item => item.total),
          recoveries: this.covidData['unofficial-summary'].map(item => item.recovered),
          deaths: this.covidData['unofficial-summary'].map(item => item.deaths),
        };
      } catch (error) {
        console.error('Error fetching data:', error);
      }
    },
  },
};
</script>

  
  <style scoped>
  .case-count {
    border: 1px solid #ccc;
    padding: 10px;
    margin: 10px 0;
  }
  
  .widget {
    border: 1px solid #ccc;
    padding: 10px;
    margin: 10px 0;
  }
  </style>
  