

<!-- src/App.vue -->
<template>
  <div id="app">
<!--  <cool_cal id ='cool_cal'> </cool_cal>-->
    <fan_power_cal id="fan_power_cal"></fan_power_cal>
  </div>
</template>

<script>

import cool_cal from './components/cool_cal.vue';
import fan_power_cal from "@/components/fan_power_cal.vue";
export default {
  data(){
    return{
      // tab_name:'ChetApp'
    }
  },
  components: {
    cool_cal,
    fan_power_cal
  },
  methods:{
    chg(n){
      this.tab_name=n
      // console.log(n)
      console.log(this.tab_name)
    }
  }

};
</script>

<style>
#app {

}
#cool_cal{
  width: 800px;
}

</style>
