<template>
    <div id="game" align="center">
        <table width="100%">
            <tr>
                <td valign="top" style="max-width: 500px">
                    <span>Наблюдаем за зайцем с номером </span>
                    <input class="rabbitId" type="text" v-model="chatId">
                    <user-list
                            :serverHost="serverHost"
                            :posts="posts"
                            v-on:setChatId="setChatId"
                    ></user-list>
                    <HelloWorld :msg="message"></HelloWorld>
                    <div v-if="currentRabbit.tacticId == 1">
                        <p>У вашего зайца тактика поиска в ширину</p>
                    </div>
                    <div v-if="currentRabbit.tacticId == 0">
                        <p>У вашего зайца нет тактики, если видит в соседней клетке траву - то ест</p>
                    </div>
                    <input type="checkbox" disabled hidden v-model="checkTactic1"/>
                    <button v-on:click="sendTactic()">Сменить тактику</button>
                    <little-map :msg="littleMapString" :capacity="capacity" :clientRabbitIndex="currentRabbit.x ? currentRabbit.y * capacity + currentRabbit.x : -1"></little-map>
                </td>
                <td valign="top">
                    <Map :map="map" :rabbits="rabbits" :chatId="chatId"></Map>
                </td>
            </tr>
        </table>
    </div>
</template>

<script>
    import HelloWorld from './HelloWorld.vue';
    import Map from './Map.vue';
    import axios from 'axios';
    import UserList from "./UserList";
    import * as config from "../config";
    import LoginPage from "./LoginPageVue";
    import LittleMap from "./LittleMap";

    let instance;
    if (config && config.config && config.config.serverHost) {
        instance  = axios.create({
            baseURL: 'http://' + config.config.serverHost + ':8090',
            timeout: 1000
        });
    } else {
        instance  = axios.create({
            baseURL: 'http://localhost:8090',
            timeout: 1000
        });
    }

    export default {
        name: 'game',
        data: function() {
            return {
                message : '',
                map: {},
                rabbits: [],
                currentRabbit: {},
                checkTactic1: false,
                chatId: 0,
                serverHost: config && config.config && config.config.serverHost ? config.config.serverHost : 'localhost',
                littleMap: '',
                mapCadrs: []
            }
        },
        props: {
            userToken: '',
            gameData: {},
            posts: [],
            littleMapString: '',
            capacity: 0
        },
        created : function() {
        },
        watch: {
            chatId: function(val){
                console.log(val);
                this.currentRabbit = this.rabbits.filter((item) => {return item.clientId == val})[0];
                this.checkTactic1 = this.currentRabbit ? this.currentRabbit.tacticId == 1 : false;
                console.log(this.currentRabbit);
            },
            gameData: function (val) {
                this.message = 'Текущее время: ' + val.gameDto.innerTime;
                this.map = val.mapDto;
                this.rabbits = val.rabbitDtoList;
                this.currentRabbit = this.rabbits.filter((item) => {return item.clientId == this.chatId})[0];
                this.checkTactic1 = this.currentRabbit ? this.currentRabbit.tacticId == 1 : false;
            }
        },
        methods: {
            sendTactic: function () {
                let _this = this;
                instance.get(`/rest/setTactic?chatId=${_this.chatId}&tacticId=${_this.checkTactic1 ? 0 : 1}`)//инверсим то есть
                    .then((response) => {
                        console.log("тактика сменена" + JSON.stringify(response));
                    });
            },
            setChatId: function (id) {
                this.chatId = id;
                this.$emit("setSocketId", id);
            },
            concatMap: function (mapCadrs) {
                return mapCadrs.join();
            }
        },
        components: {
            LittleMap,
            LoginPage,
            UserList,
            HelloWorld,
            Map
        }
    }
</script>

<style>
    #game {
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
