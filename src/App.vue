<template>
  <div id="app" align="center">
    <div>
      <div>Параметры подключения</div>
      <div v-if="getConnected()">
        <span>socketClientId: <input type="text" class="username" title="socketClientId" v-model="socketClientId"></span>
        <button data-tooltip="Отправить" @click="sendSocket">Отправить</button>
        <button data-tooltip="Переподписаться" @click="resubscribe">Переподписаться</button>
        <button data-tooltip="Отключиться" @click="disconnectSocket">Отключиться</button>
      </div>
      <div v-if="!getConnected()">
        <button data-tooltip="Подключиться" @click="connectSocket">Подключиться</button>
      </div>
    </div>
    <div v-if="userToken.length == 0">
      <login-page v-on:setLogin = "setLogin" v-on:setPassword = "setPassword" v-on:getToken = "getToken" v-on:setToken = "setToken"></login-page>
      <div v-if="message && message.length">
        <span>{{message}}</span>
        <button class="errorMessage" data-tooltip="Удалить" @click="message = ''">х</button>
      </div>
    </div>
    <div v-if="userToken.length > 0">
      <game :userToken="userToken" v-on:setSocketId="setSocketId"></game>
    </div>
  </div>
</template>

<script>
    import axios from 'axios';
    import * as config from "./config";
    import SockJS from 'sockjs-client';
    import Stomp from 'stompjs';
    import LoginPage from "./components/LoginPageVue";
    import Game from "./components/GameComponent.vue";

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
        name: 'app',
        data: function() {
            return {
                message : '',
                map: {},
                rabbits: [],
                currentRabbit: {},
                checkTactic1: false,
                chatId: 0,
                serverHost: config && config.config && config.config.serverHost ? config.config.serverHost : 'localhost',
                userToken: '',
                login: '',
                password: '',
                socket: {},
                stompClient: {},
                socketClientId: 0,
                stompSubscribe: {}
            }
        },
        created : function() {
            let _this = this;
            this.socket = new SockJS("http://localhost:8090/gs-guide-websocket");
            this.stompClient = Stomp.over(this.socket);
            this.stompClient.connect({}, function (frame) {
                //setConnected(true);
                console.log('Connected: ' + frame);
                _this.stompSubscribe = _this.stompClient.subscribe('/topic/' + _this.socketClientId, _this.receiveMessage);
            });

        },
        watch: {

        },
        methods: {
            getToken: function () {
                let _this = this;
                instance.get(`/login?username=${_this.login}&password=${_this.password}`)
                    .then((response) => {
                        if (response && response.data && response.data.token){
                            _this.userToken = response.data.token;
                            _this.message = '';
                            _this.stompClient.send("/app/" + _this.socketClientId, {}, JSON.stringify({content:response.data.token}));
                        } else {
                            _this.message = 'Проблема. Неверный пользователь или пароль.';
                        }
                    });
            },
            setLogin: function(value){
                this.login = value;
            },
            setPassword: function (value){
                this.password = value;
            },
            setToken: function (value){
                this.userToken = value;
            },
            sendSocket: function () {
                this.stompClient.send("/app/" + this.socketClientId, {}, JSON.stringify({chatId: 0}));
            },
            resubscribe: function () {
                this.stompSubscribe.unsubscribe();
                this.stompSubscribe = this.stompClient.subscribe('/topic/' + this.socketClientId, this.receiveMessage);
            },
            receiveMessage: function (greeting) {
                console.log('Receive: ' + greeting.body);
                // _this.showGreeting(JSON.parse(greeting.body).content);
            },
            getConnected: function () {
                return this.stompClient.connected;
            },
            connectSocket: function () {
                let _this = this;
                this.socket = new SockJS("http://localhost:8090/gs-guide-websocket");
                this.stompClient = Stomp.over(this.socket);
                this.stompClient.connect({}, function (frame) {
                    //setConnected(true);
                    console.log('Connected: ' + frame);
                    _this.stompSubscribe = _this.stompClient.subscribe('/topic/' + _this.socketClientId, _this.receiveMessage);
                });
            },
            disconnectSocket: function () {
                let _this = this;
                if (this.stompSubscribe && this.stompSubscribe.unsubscribe) this.stompSubscribe.unsubscribe();
                this.stompClient.disconnect(function () {
                    console.log('Disconnected: ');
                    _this.stompSubscribe = {};
                });
                this.socket.close(1000, "User disconnect");
            },
            setSocketId: function (id) {
                this.socketClientId = id;
                this.resubscribe();
            }
        },
        components: {
            LoginPage,
            Game
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

</style>
