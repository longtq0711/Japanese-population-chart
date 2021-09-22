<template>
  <div>
    <line-chart 
      :chart-data= datacollection
    ></line-chart>
  </div>
</template>

<script>
  import LineChart from './PopulationChart.js'

  export default {
    components: {
      LineChart
    },
    data () {
      return {
        datacollection: null,
        loaded: false,
        temperature: [0,1,2,3,4,5,6,7,8,9],
        humidity: [9,8,7,6,5,4,3,2,1,0],
        potentio: [4,5,4,5,4,5,4,5,4,5],
        time: [0,1,2,3,4,5,6,7,8,9]
      }
    },
    async mounted () {
      this.fillData();
      console.log(this.datacollection);
    },
    methods: {
      fillData () {
        this.datacollection = {
          labels: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9],
          datasets: [
            {
              label: 'Temp (°C)',
              backgroundColor: 'rgba(0, 0, 255, 0.2)',
              borderColor: 'lightblue', 
              pointBackgroundColor: 'blue', 
              borderWidth: 1, 
              pointBorderColor: 'blue',
              data: [1,2,3,2,1,3,4,6,7,8]
              // data: this.temperature
            }, 
          ]
        }
      }
    }
  }
</script>

<style>
  .small {
    width: 500px;
    height: 100px;
    margin: auto
  }
</style>