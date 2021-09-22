<template>
  <div id="app">
    <img alt="Vue logo" src="./assets/logo.png">
    <h1>都道府県</h1>
    <label v-for="(item, key) in prefectures" :key="key">
      <input type="checkbox">
      {{ item.prefName }}
    </label>
    <PopulationChart></PopulationChart>
  </div>
</template>

<script>
import axios from "axios";
import PopulationChart from "./components/PopulationChart.vue";
export default {
  name: 'App',
  components:{
    PopulationChart
  },
  data() {
    return {
      prefectures: []
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