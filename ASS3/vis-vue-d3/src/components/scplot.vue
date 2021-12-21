<template>
  <div class="vis-component" ref="chart">
    <svg class="main-svg" :width="svgWidth" :height="svgHeight">
      <g class="chart-group" ref="chartGroup">
        <g class="colorscheme" ref="rects"></g>
        <g class="axis axis-x" ref="axisX"></g>
        <g class="axis axis-y" ref="axisY"></g>
        <g class="brush" ref="brush"></g>
        <g class="scatterplot" ref="dots"></g>
      </g>
    </svg>
  </div>
</template>

<script>

import * as d3 from 'd3';

export default {
  name: 'Scatterplot',
  props: {
  },
  data() {
    return {
      svgWidth: 500,
      svgHeight: 500,
      svgPadding: {
        top: 20, right: 20, bottom: 20, left: 20,
      },
    }
  },
  mounted() {
    this.drawChart();
  },
  methods: {
    // this method draws the whole chart and calls every other method
    drawChart() {
      if (this.$refs.chart) this.svgWidth = this.$refs.chart.clientWidth;
      d3.select(this.$refs.chartGroup)
        .attr('transform', `translate(${this.svgPadding.left},${this.svgPadding.top})`);
      this.drawRect();
      this.drawXAxis();
      this.drawYAxis();
      this.drawScatterplot();
    },
    // draws x axis
    drawXAxis() {
      d3.select(this.$refs.axisX)
        .attr('transform', `translate(20, ${this.svgHeight - this.svgPadding.top - this.svgPadding.bottom} )`)
        .call(d3.axisBottom(this.xScale))
        .append('text')
        .attr('x', 460)
        .attr('dy', '-0.71em')
        .attr('text-anchor', 'end') 
        .attr('fill', 'black')
        .text("Educational Attainment: Bachelor's Degree or Higher (%)");
    },
    // draws Y axis
    drawYAxis() {
      d3.select(this.$refs.axisY)
        .attr('transform', `translate( 20, 0 )`)
        .call(d3.axisLeft(this.yScale))
        .append('text')
        .attr('transform', 'rotate(-90)')
        .attr('y', 6)
        .attr('dy', '0.71em')
        .attr('text-anchor', 'end') 
        .attr('fill', 'black')
        .text("Average personal yearly income in a State");
    },
    // draws the datapoints
    drawScatterplot(){
      const zip = d3.zip(this.educationRates, this.personalIncome);
      const dots = d3.select(this.$refs.dots);
          dots.selectAll('circle')
            .data(zip)
            .join('circle')
            .attr('class', 'scatterplot')
            .attr('cx', d => this.xScale(d[0].value))
            .attr('cy', d => this.yScale(d[1].value))
            .attr('r', 4)
            .attr("id", function(d) { return ('' + d[0].state).replace(/\s/g, '');})
            .attr('transform', `translate( 20, 0 )`)
            .style('stroke', 'gray')
            .style('fill', 'ghostwhite')
            .on('mouseover', this.showName)
            .on('mouseout', this.removeName)

      // Brush
      d3.select(this.$refs.brush)
                .append('g')
                .attr('class', 'brush')
                .call(this.brush)

    },
    // handles on mouse over event
    showName(event, d){
        d3.select(('#'+d[0].state).replace(/\s/g, ''))
          .attr('r', 5)
          .style('fill','honeydew')

        d3.select(this.$refs.dots)
          .append('text')
          .text(d[0].state)
          .attr("x", this.xScale(d[0].value))
          .attr("y", this.yScale(d[1].value))
          .attr("dy", "-.7em")
          .attr("id", "stateName")
    },
    // handles on mouse out event
    removeName(event, d){
        d3.select(('#'+d[0].state).replace(/\s/g, ''))
          .attr('r', 4)
          .style('fill','ghostwhite')

        d3.select("#stateName").remove();
    },
    // draws the color pallete behind the plot
    drawRect(){
      const collorPallete = [
        ["#4885C1", "#435786", "#3F2949"],
        ["#89A1C8", "#806A8A", "#77324C"],
        ["#CABED0", "#BC7C8F", "#AE3A4E"],
        ];
      
      for(let i = 0; i < 3; i++){
        for(let j = 0; j < 3; j++){
          const rects = d3.select(this.$refs.rects);
          rects.selectAll('.rect')
            .data(this.educationRates)
            .join('rect')
            .attr('class', 'colorscheme')
            .attr('x', (this.svgPadding.left) + (i*460/3))
            .attr('y', (j*460/3))
            .attr('width', 460/3)
            .attr('height',460/3)
            .attr('fill', collorPallete[j][i])
        }
      }
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
    // compute max educationRate
    eduMax() {
      return d3.max(this.educationRates, (d) => d.value);
    },
    // compute min educationRate
    eduMin() {
      return d3.min(this.educationRates, (d) => d.value);
    },
    // compute max incomeRate
    incomeMax() {
      return d3.max(this.personalIncome, (d) => d.value);
    },
    // compute min incomeRate
    incomeMin() {
      return d3.min(this.personalIncome, (d) => d.value);
    },
    // compute the scale of x axis
    xScale() {
      return d3.scaleLinear()
        .domain([this.eduMin, this.eduMax])
        .range([0, this.svgHeight - this.svgPadding.top - this.svgPadding.bottom])
    },
    // compute the scale of x axis
    yScale() {
      return d3.scaleLinear()
        .domain([this.incomeMin, this.incomeMax])
        .range([this.svgHeight - this.svgPadding.top - this.svgPadding.bottom, 0])
    },
    // handles the brush call
    brush() {
      let scaleX = this.xScale;
      let scaleY = this.yScale;
      let store = this.$store;
      return d3.brush()
          .extent([
              [20, 0],
              [480, 460]
          ])
          .on("start brush", function (e) {
            let selection = e.selection
            
            if(selection){
              let x0 = scaleX.invert(selection[0][0] - 20) 
              let x1 = scaleX.invert(selection[1][0] - 20)
              let y0 = scaleY.invert(selection[1][1])
              let y1 = scaleY.invert(selection[0][1])

              const selectedStates = d3.selectAll('circle')
                              .filter(d=> (d[0].value >= x0 && d[0].value <= x1)
                                      && (d[1].value >= y0 && d[1].value <= y1));

              const selectedStatesIDs = []

              selectedStates.each(function() {
                const temp = d3.select(this)
                selectedStatesIDs.push(temp.attr('id'))
              })
              
              store.commit('changeSelectedState', selectedStatesIDs)
            }else{
              store.commit('changeSelectedState', [])
            }
            
        })
    }
  },
  watch: {
    educationRates: {
      handler() {
        this.drawChart();
      },
      deep: true,
    },
    personalIncome: {
      handler() {
        this.drawChart();
      },
      deep: true,
    },
  },
}
</script>

<style>
</style>
