<template>
    <div class="widget">
      <h2>{{ title }}</h2>
      <div id="chart-container"></div>
    </div>
  </template>
  
  <script>
  import c3 from 'c3';
  
  export default {
    props: {
      title: String,
      chartData: Object,
      chartType: String,
    },
    mounted() {
      this.createChart(this.chartData, this.chartType);
    },
    methods: {
      createChart(data, chartType) {
        c3.generate({
          bindto: '#chart-container',
          data: {
            x: 'x',
            columns: [
              ['x', ...data.locations], // Change 'dates' to 'locations'
              ['Cases', ...data.cases],
              ['Recoveries', ...data.recoveries],
              ['Deaths', ...data.deaths],
            ],
            type: chartType, // Use the selected chart type
          },
          axis: {
            x: {
              type: 'category',
            },
          },
        });
      },
  
      // Method to update chart type
      updateChartType(newChartType) {
        this.createChart(this.chartData, newChartType);
      },
    },
  };
  </script>
  
  

