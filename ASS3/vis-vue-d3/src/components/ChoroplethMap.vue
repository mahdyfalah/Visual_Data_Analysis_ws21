<template>
  <div class="vis-component" ref="chart">
    <div class="placeholder">
      <!-- <p>Selected states by clicking on the bar chart: {{ selectedStates }}</p> -->
    </div>
    <svg class="main-svg" :width="svgWidth" :height="svgHeight">
      <g class="usaMap" ref="choropleth"></g>
    </svg>
  </div>
</template>

<script>

import mapStatesUSA from '@/assets/us-states-geo.json';
import * as d3 from 'd3';

export default {
  name: 'ChoroplethMap',
  props: {
  },
  data() {
    return {
      svgWidth: 700,
      svgHeight: 500,
      svgPadding: {
        top: 20, right: 0, bottom: 20, left: 0,
      },
    }
  },
  mounted() {
    // Use the following map geoJSON object ("mapStatesUSA") for your projection
    // console.log(mapStatesUSA);
    this.drawMap();
  },
  methods: {
    // draws the map as different paths using the given mapStatesUSA and d3.geoAlbersUsa()
    drawMap(){

      const prj = d3.geoAlbersUsa().scale([700 * 1.25])

      const path = d3.geoPath().projection(prj)

      const svg = d3.select(this.$refs.choropleth)
      
      //  stores All state IDs 
      const allStateIDs = []
      for(let i=0; i<51; i++){
          allStateIDs.push(mapStatesUSA.features[i].properties.name.replace(/\s/g, ''))
        }

      // draws the map
      svg.selectAll('path')
        .data(mapStatesUSA.features)
        .enter()
        .append('path')
        .attr('d', path)
        .attr('class', 'map')
        .attr("id", function(d, i) {return mapStatesUSA.features[i].properties.name.replace(/\s/g, '')})
        .attr('transform', `translate( -150, 0 )`)
        .style('stroke', 'ghostwhite')
        .style('stroke-width', 1)


      
      const zip = d3.zip(this.educationRates, this.personalIncome);
    

      // to check if zip and selectedStates are empty and check different coloring conditions
      if(zip.length){
        if(!this.selectedStates.length){
          for(let i=0; i<51; i++){
            svg.select('#' + allStateIDs[i])
              .style('fill', this.color(zip[i][0].value, zip[i][1].value));
          }
        }else{
          if(!this.selectedStates[this.selectedStates.length - 1].length){
            for(let i=0; i<51; i++){
              svg.select('#' + allStateIDs[i])
                .style('fill', this.color(zip[i][0].value, zip[i][1].value));
            }
          }else{
            for(let i=0; i<51; i++){
              if(this.selectedStates[this.selectedStates.length - 1].includes(allStateIDs[i])){
                svg.select('#' + allStateIDs[i])
                .style('fill', this.color(zip[i][0].value, zip[i][1].value));
              }else{
                svg.select('#' + allStateIDs[i])
                  .style('fill', 'WhiteSmoke');
              }
            }
          } 
        }
      }
      console.log(this.selectedStates)
    },

    // choose the correct color for each state
    color(i, j) {
        const collorPallete = [
        ["#CABED0", "#89A1C8", "#4885C1"],
        ["#BC7C8F", "#806A8A", "#435786"],
        ["#AE3A4E", "#77324C", "#3F2949"],
        ];

      let xIndex = Math.floor((i - this.eduMin) / ((this.eduMax - this.eduMin)/3))

      let yIndex = Math.floor((+j - this.incomeMin)/ ((this.incomeMax - this.incomeMin)/3))
      
      return collorPallete[xIndex>2?2:xIndex][yIndex>2?2:yIndex]
      }
    
  },
  computed: {
    educationRates: {
      get() {
        return this.$store.getters.educationRates;
      }
    },
    personalIncome: {
      get() {
        return this.$store.getters.personalIncome;
      }
    },
    selectedStates: {
      get() {
        return this.$store.getters.selectedStates;
      }
    },
    eduMax() {
      return d3.max(this.educationRates, (d) => d.value);
    },
    eduMin() {
      return d3.min(this.educationRates, (d) => d.value);
    },
    incomeMax() {
      return d3.max(this.personalIncome, (d) => d.value);
    },
    incomeMin() {
      return d3.min(this.personalIncome, (d) => d.value);
    }
  },
  watch: {
    educationRatesAndIncome: {
      handler() {
        this.drawMap();
      },
      deep: true,
    },
    personalIncome: {
      handler() {
        this.drawMap();
      },
      deep: true,
    },
    selectedStates: {
      handler() {
        this.drawMap();
      },
      deep: true,
    },
  },
}
</script>

<style>
</style>
