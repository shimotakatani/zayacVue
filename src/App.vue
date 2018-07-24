<template>
  <div id="app" align="center">
    <span>Наблюдаем за зайцем с номером </span>
    <input class="rabbitId" type="text" v-model="chatId">
    <HelloWorld :msg="message"/>
    <Map :map="map" :rabbits="rabbits" :chatId="chatId"></Map>
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
                map: {},
                rabbits: [],
                chatId: 0
            }
        },
        created : function() {
            let _this = this;
            setInterval(function () {
                instance.get('/rest/game?chatId=' + _this.chatId)
                    .then((response) => {
                        _this.message = 'Текущее время: ' + response.data.gameDto.innerTime;
                        _this.map = JSON.parse(JSON.stringify(response.data.mapDto));
                        _this.rabbits = JSON.parse(JSON.stringify(response.data.rabbitDtoList));
                    })
            }, 1000);
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
    margin-top: 20px;
  }

  .rabbitId {
    margin-right: 10px;
    margin-left: 10px;
    max-width: 100px;
    min-width: 30px;
    font-size: 18px;
    text-align: center
  }

</style>
