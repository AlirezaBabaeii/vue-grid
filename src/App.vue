<template>
  <div id="app">
<h3>al hadi</h3>
    <!-- <HelloWorld msg="Welcome to Your Vue.js App"/> -->
    <Tabel/>
  </div>
</template>

<script>
// import HelloWorld from './components/HelloWorld.vue'
import Tabel from './components/tabel.vue'
export default {
  name: 'App',
  components: {
    // HelloWorld,
    Tabel
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
