<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <h1>都道府県</h1>
    <label v-for="(item, key) in prefectures" :key="key">
      <input type="checkbox" :value="key" v-model="checkedValue" v-on:click="getData(item.prefName, item.prefCode, $event)">
      {{ item.prefName }}
    </label>
    <!-- <span>Checked value: {{ checkedValue }}</span> -->
    <line-chart 
      v-if="loaded"
      :chart-data="datacollection"
      :options="options" 
    ></line-chart>
  </div>
</template>

<script>
import axios from "axios";
import LineChart from './components/PopulationChart.js'

export default {
  name: 'App',
  components:{
    LineChart
  },
  data () {
    return {
      datacollection: null,
      loaded: false,
      options: {
        responsive: true,
        maintainAspectRatio: false
      },
      population: [],
      years: [],
      prefectures: [],
      populations: null,
      dataset: [],
      checkedValue: []
    }
  },
  async created() {
    const APIKEY = "aOxiRQPVhQ3Qxzs55UWDKo60M51tRnEY46QyIEm1";
    const headers = {
      'X-API-KEY':APIKEY
    }
    let scop = this;
    axios({
      method: 'GET',
      url: 'https://opendata.resas-portal.go.jp/api/v1/prefectures',
      headers: headers
    }).then(response => {
      let data = response.data;
      scop.prefectures = data.result;
    }).catch(error => {
      console.log(error.message);
    })
  },
  methods: {
    getData(prefNameValue, prefCodeValue, event) {
      if(event.target.checked) {
        const APIKEY = "aOxiRQPVhQ3Qxzs55UWDKo60M51tRnEY46QyIEm1";
        const headers = {
          'X-API-KEY':APIKEY
        }
        let scop = this;
        axios({
          method: 'GET',
          url:'https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear?prefCode=' + prefCodeValue,
          headers: headers,
        }).then(response => {
          let value = response.data.result.data;
          scop.populations = value[0].data;
          scop.population = [''];
          scop.years = [''];
          for (let i = 0; i < scop.populations.length; i+=2) {
            let year = scop.populations[i].year;
            if(year % 2 == 0) {
              scop.years.push(year);
              scop.population.push(scop.populations[i].value);
            }
            if (year == 2020) {
              break;
            }
          }
          this.dataset.push({
            label: prefNameValue,
            borderColor: 'blue', 
            pointBackgroundColor: 'blue', 
            borderWidth: 1, 
            pointBorderColor: 'blue',
            data: this.population
          }); 
          this.fillData();
        }).catch(error => {
          console.log(error.message);
        })
      } else {
        let found = this.dataset.findIndex(function(element) {
          return element.label == prefNameValue;
        });
        this.dataset.splice(found,1);
        this.fillData();
      }
    },
    fillData () {
      this.datacollection = {
        labels: this.years,
        datasets: this.dataset
      }
      if(this.dataset.length > 0) {
        this.loaded = true;
      } else {
        this.loaded = false;
      }
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>