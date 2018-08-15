<template>
    <div>
        <span class="login_text">
            Введите пожалуйста логин и пароль:
        </span>
        <div>
            <span>Логин: <input type="text" class="username" title="логин" v-model="login"></span>
        </div>
        <div>
            <span>Пароль: <input type="password" class="password" title="пароль" v-model="password"></span>
        </div>
        <button @click="emitLogin">Войти</button>
    </div>
</template>

<script>
    export default {
        name: 'LoginPage',
        data: function (){
            return {
                login: '',
                password: ''
            }
        },
        methods: {
            emitLogin: function () {
                //todo возможно переделать на 1 событие с двумя параметрами
                this.$emit("setLogin", this.login);
                this.$emit("setPassword", this.password);
                this.$emit("getToken");
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    h3 {
        margin: 40px 0 0;
    }

    ul {
        list-style-type: none;
        padding: 0;
    }

    li {
        display: inline-block;
        margin: 0 10px;
    }

    a {
        color: #42b983;
    }
</style>