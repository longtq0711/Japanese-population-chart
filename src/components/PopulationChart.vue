<template>
  <div>
    
  </div>
</template>

<script>
  // import axios from "axios"
  
  export default {
    components: {
      LineChart
    },
    props: {
      years:{
        type: Object,
        default: null
      },
      populations: {
        type: Object, Array,
        default: null
      }
    },
    
    updated() {
      console.log(this.populations);
    },
    async mounted () {
      this.fillData();
      console.log(this.populations);
    },
    methods: {
     
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