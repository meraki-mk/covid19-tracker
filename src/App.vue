<template>
  <div>
    <h1>Covid Dashboard</h1>
  <dashboardComponent />
  </div>
</template>


<script>
import dashboardComponent from './components/DashboardComponent.vue';
export default {
  name: "App",
  components: {
    dashboardComponent
  }
};
</script>


<style>
body {
  border-color: #000;
}
</style>
