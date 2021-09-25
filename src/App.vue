<template>
  <div id="app">
    <h3>都道府県</h3>
    <div class="checkbox-list">
      <div v-for="(item, key) in prefectures" :key="key" class="checkbox-item">
        <label class="custom-checkbox">
          <span>{{ item.prefName }}</span>
          <input type="checkbox" v-on:click="getData(item.prefName, item.prefCode, $event)">
          <span class="checkmark"></span>
        </label>
      </div>
    </div>
    <line-chart
      v-if="loaded"
      :chart-data="datacollection"
      :options="options"
    ></line-chart>
  </div>
</template>

<script>
import axios from 'axios'
import LineChart from './components/PopulationChart.js'

export default {
  name: 'App',
  components: {
    LineChart
  },
  data () {
    return {
      datacollection: null,
      loaded: false,
      options: {
        scales: {
          yAxes: [{
            ticks: {
              beginAtZero: true
            },
            gridLines: {
              color: 'rgba(0, 0, 0, 0)'
            }
          }],
          xAxes: [{
            gridLines: {
              color: 'rgba(0, 0, 0, 0)'
            }
          }]
        },
        responsive: true,
        maintainAspectRatio: false
      },
      population: [],
      years: [],
      chartcolor: null,
      prefectures: [],
      populations: null,
      dataset: []
    }
  },
  async created () {
    const APIKEY = 'aOxiRQPVhQ3Qxzs55UWDKo60M51tRnEY46QyIEm1'
    const headers = {
      'X-API-KEY': APIKEY
    }
    const scop = this
    axios({
      method: 'GET',
      url: 'https://opendata.resas-portal.go.jp/api/v1/prefectures',
      headers: headers
    }).then(response => {
      const data = response.data
      scop.prefectures = data.result
    }).catch(error => {
      console.log(error.message)
    })
  },
  methods: {
    getData (prefNameValue, prefCodeValue, event) {
      //Check if checkbox is checked than get data
      if (event.target.checked) {
        const APIKEY = 'aOxiRQPVhQ3Qxzs55UWDKo60M51tRnEY46QyIEm1'
        const headers = {
          'X-API-KEY': APIKEY
        }
        const scop = this
        axios({
          method: 'GET',
          url: 'https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear?prefCode=' + prefCodeValue,
          headers: headers
        }).then(response => {
          const value = response.data.result.data
          scop.populations = value[0].data
          scop.population = []
          scop.years = ['']
          // add data to show at xAxios and yAxios of chart
          for (let i = 0; i < scop.populations.length; i += 2) {
            const year = scop.populations[i].year
            if ((year >= 1970) && (year % 2 === 0)) {
              if (year >= 1980) scop.years.push(year)
              scop.population.push(scop.populations[i].value)
            }
            if (year === 2020) {
              break
            }
          }
          this.mycolor = '#' + (Math.random() * 0xFFF3FF << 0).toString(16) // set random color for chart
          this.dataset.push({
            label: prefNameValue,
            borderColor: this.mycolor,
            pointBackgroundColor: this.mycolor,
            borderWidth: 1,
            pointBorderColor: this.mycolor,
            fill: false,
            data: this.population
          })
          this.fillData()
        }).catch(error => {
          console.log(error.message)
        })
      } else { //Else splice that one
        const found = this.dataset.findIndex(function (element) {
          return element.label === prefNameValue
        })
        this.dataset.splice(found, 1)
        this.fillData()
      }
    },
    fillData () {
      this.datacollection = {
        labels: this.years,
        datasets: this.dataset
      }
      if (this.dataset.length > 0) {
        this.loaded = true
      } else {
        this.loaded = false
      }
    }
  }
}
</script>

<style>
  @import './assets/style.css';
</style>
