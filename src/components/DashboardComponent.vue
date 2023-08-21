<template>
  <div>
    <h2>Covid-19 Tracker</h2>
    <div v-if="covidData">
      <!-- Widgets -->
      <WidgetComponent 
        :title="totalCasesWidget.title" 
        :value="totalCasesWidget.value"
        @init-chart="initChart" 
        ref="widgetComponent"
      />
      <WidgetComponent 
        :title="totalDeathsWidget.title" 
        :value="totalDeathsWidget.value" 
        @init-chart="initChart" 
        ref="widgetComponent"
      />

      <select v-model="selectedChartType" @change="updateChartType">
        <option value="line">Line Chart</option>
        <option value="bar">Bar Chart</option>
        <option value="area">Area Chart</option>
      </select>

      <!-- Location Filter -->
      <label for="location">Location:</label>
      <select v-model="selectedLocation" @change="applyFilters">
        <option value="">All</option>
        <option v-for="region in uniqueLocations" :key="region">{{ region }}</option>
      </select>

      <!-- <WidgetChartComponent :title="chartWidget.title" :chartData="chartData" /> -->
      <WidgetChartComponent 
        :title="chartWidget.title" 
        :chartData="chartData" 
        :chartType="selectedChartType" 
        ref="chartComponent" 
      />
    
      <h2>Regional Data</h2>
      <table>
        <thead>
          <tr>
            <th>Location</th>
            <th>Total Cases</th>
            <th>Recovered</th>
            <th>Deaths</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="region in covidData.regional" :key="region.loc">
            <td>{{ region.loc }}</td>
            <td>{{ region.totalConfirmed }}</td>
            <td>{{ region.discharged }}</td>
            <td>{{ region.deaths }}</td>
          </tr>
        </tbody>
      </table>
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
      chartData: {}, 
      selectedChartType: 'line',
      selectedLocation: '',
    };
  },
  created() {
    this.fetchCovidData();
  },
  computed: {
    uniqueLocations() {
      return ['All', ...new Set(this.covidData.data['regional'].map(region => region.loc))];
    },
    filteredRegionalData() {
      if (this.selectedLocation === 'All' || this.selectedLocation === '') {
        return this.covidData.data['regional'];
      } else {
        return this.covidData.data['regional'].filter(region => region.loc === this.selectedLocation);
      }
    },
  },

  methods: {
    async fetchCovidData() {
      try {
        const response = await axios.get('https://api.rootnet.in/covid19-in/stats/latest');
        this.covidData = response.data; // Assign the entire response data

        // Update widget values
        this.totalCasesWidget.value = this.covidData.data.summary.total;
        this.totalDeathsWidget.value = this.covidData.data.summary.deaths;

        // Calculate chart data
        this.chartData = {
          cases: this.covidData.data['unofficial-summary'].map(item => item.total),
          recoveries: this.covidData.data['unofficial-summary'].map(item => item.recovered),
          deaths: this.covidData.data['unofficial-summary'].map(item => item.deaths),
          locations: this.covidData.data['unofficial-summary'].map(item => item.source), // Assuming this is the location data
        };

        // Initialize chart
        this.applyFilters();
      } catch (error) {
          console.error('Error fetching data:', error);
      }
    },
    initChart() {
      // Initialize charts
      this.$refs.chartComponent.createChart(this.chartData);
    },
    updateChartType() {
      this.initChart(); // Call this to update the chart
      this.$refs.chartComponent.updateChartType(this.selectedChartType);
    },
    applyFilters() {
      // Update chart data based on selected location
      this.chartData = {
        cases: this.filteredRegionalData.map(region => region.totalConfirmed),
        recoveries: this.filteredRegionalData.map(region => region.discharged),
        deaths: this.filteredRegionalData.map(region => region.deaths),
        locations: this.filteredRegionalData.map(region => region.loc), // Change 'dates' to 'locations'
      };
      
      // Update chart
      this.initChart(); // Call this to update the chart
      this.$refs.chartComponent.$emit("init-chart"); // Emit the event
    },
  },

    

  };
</script>


<style scoped>

</style>
