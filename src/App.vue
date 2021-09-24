<template>
  <div id="app">
    <div class="checkbox-list">
      <h3>都道府県</h3>
      <div v-for="(item, key) in prefectures" :key="key" class="checkbox-item">
        <label class="custom-checkbox">
          {{ item.prefName }}
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
         scales: {
          yAxes: [{
            ticks: {
              beginAtZero: true
            }
          }]
        },
        responsive: true,
        maintainAspectRatio: false
      },
      population: [],
      years: [],
      chartcolor:null,
      prefectures: [],
      populations: null,
      dataset: [],
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
          scop.population = [];
          scop.years = [];
          for (let i = 0; i < scop.populations.length; i+=2) {
            let year = scop.populations[i].year;
            if((year >= 1980) && (year % 2 == 0)) {
              scop.years.push(year);
              scop.population.push(scop.populations[i].value);
            }
            if (year == 2020) {
              break;
            }
          }
          this.mycolor = '#'+(Math.random()*0xFFFFFF<<0).toString(16); //set random color for chart
          this.dataset.push({
            label: prefNameValue,
            borderColor: this.mycolor, 
            pointBackgroundColor: this.mycolor, 
            borderWidth: 1, 
            pointBorderColor: this.mycolor,
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
.checkbox-list {
  display: flex;
  flex-wrap: wrap;
}
.checkbox-list h3 {
  flex: 0 0 100%;
  max-width: 100%;
}
.checkbox-list .checkbox-item{
  flex: 0 0 16.66667%;
  max-width: 16.66667%;
}
/* start: custom-checkbox */
.custom-checkbox {
    display: block;
    position: relative;
    padding-left: 25px;
    cursor: pointer;
    font-weight: normal;
    word-wrap: break-word;
    margin-bottom: .5rem;
}
/* Hide the browser's default checkbox */
.custom-checkbox input {
    position: absolute;
    opacity: 0;
    cursor: pointer;
    height: 0;
    width: 0;
}
.custom-checkbox .checkmark {
    position: absolute;
    top: 2px;
    left: 50px;
    height: 18px;
    width: 18px;
    border: 1.5px solid #000000;
}

.custom-checkbox .checkmark:after {
    content: "";
    position: absolute;
    display: none;
}
.custom-checkbox input:checked ~ .checkmark:after {
    display: block;
}
.custom-checkbox .checkmark:after {
    content: '\2714';
    font-size: 17px;
    position: absolute;
    top: -2px;
    left: 3px;
    color: #000000;
}
</style>