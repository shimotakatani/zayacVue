<template>
  <div id="app">
    <p>Привет</p>
    <img src="./assets/logo.png">
    <HelloWorld :msg="message"/>
    <Map :map="map"></Map>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue';
import Map from './components/Map.vue';
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
      map: {}
    }
  },
  created : function() {
      let _this = this;
      setInterval(function () {
          instance.get('/rest/game?chatId=0')
              .then((response) => {
                  _this.message = 'Текущее время: ' + response.data.gameDto.innerTime;
                  _this.map = JSON.parse(JSON.stringify(response.data.mapDto));
              })
      }, 2000);
  },
  components: {
    HelloWorld,
      Map
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
