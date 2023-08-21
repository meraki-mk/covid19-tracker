<template>
    <div class="widget">
      <h2>{{ title }}</h2>
      <p>{{ value }}</p>
    </div>
  </template>

<script>
export default {
  props: {
    title: String,
    value: Number,
  },
  mounted() {
    // Emit an event to initialize the chart in the parent component
    this.$emit("init-chart");
  },
};
</script>


<style scoped>

</style>