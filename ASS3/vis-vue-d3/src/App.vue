<template>
  <div id="app">
    <h1>A comparison of US states by education and yearly income</h1>
    <div class="container-fluid">
      <div class="row">
        <div class="col-md-12">
          <YearSlider/>
        </div>
      </div>
      <div class="row">
        <div class="col-md-5">
          <Scatterplot/>
        </div>
        <div class="col-md-7">
          <ChoroplethMap/>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// import BarChart from './components/BarChart.vue';
import Scatterplot from './components/scplot.vue'
import ChoroplethMap from './components/ChoroplethMap.vue';
import YearSlider from './components/YearSlider.vue';


export default {
  name: 'App',
  components: {
    Scatterplot, ChoroplethMap, YearSlider
  },
  mounted() {
    this.$store.dispatch('loadData');
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
</style>
