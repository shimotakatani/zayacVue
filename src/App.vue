<template>
  <div id="app">
    <img src="./assets/logo.png">
    <HelloWorld :msg="message"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue';
import axios from 'axios';
const instance = axios.create({
  baseURL: 'http://localhost:8090',
  timeout: 1000
});

export default {
  name: 'app',
  data: function() {
    return {
      message : '',
      map: ''
    }
  },
  created : function() {
    instance.get('/rest/test')
    .then((response) => {
      this.message = response.data.message;
      this.map = response.data.mapDto;
    })
.catch((error) => console.log(error));
},
  components: {
    HelloWorld
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
  margin-top: 60px;
}
</style>
