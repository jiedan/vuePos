<template>
  <div id="app">
    <leftNav></leftNav>

    <div class="main">
      <router-view></router-view>
    </div>

  </div>
</template>

<script>
  import leftNav from '@/components/common/leftNav'
  export default {
    name: 'app',
    components:{
      leftNav: leftNav
    }
  }
</script>

<style>
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
  }
  .main{
    float: left;
    width: 95%;
    text-align: left;
    background-color: #EFF2F7;

  }
</style>
