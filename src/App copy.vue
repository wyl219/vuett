

<!-- src/App.vue -->
<template>
  <div id="app">
    <Navbar @chg="chg" ></Navbar>

<!--    <br>-->
    <Home v-show="this.tab_name==='Home'" ></Home>
    <ChetApp v-show="this.tab_name==='ChetApp'" ></ChetApp>
  </div>
</template>

<script>

// import Home from './components/Home.vue';
import Home from "@/components/Home.vue";
import Navbar from "@/components/Navbar.vue";
import ChetApp from "@/components/ChetApp.vue";
export default {
  data(){
    return{
      tab_name:'ChetApp'
    }
  },
  components: {
    Home,
    Navbar,
    ChetApp
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
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 100vh;
}

#app > :first-child {
  position: fixed;
  top: 0;
  width: 800px;
}

#app > :last-child {
  /* position: fixed; */
  /* bottom: 0; */
  width: 800px;
  /* align-self: center; */
  margin: auto;

} 

</style>
