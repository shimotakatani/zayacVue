<template>
    <div class="hello">
        <div v-if="posts && posts.length" style="display: list-item">
            <table align="center">
                <tr>
                    <th>Место</th>
                    <th>#</th>
                    <th>Заяц</th>
                    <th>x</th>
                    <th>y</th>
                    <th>Жирок</th>
                    <th>Сон(%)</th>
                    <th>Действие</th>
                </tr>
                <tr v-for="(post, index) in orderedUsers">
                    <td>{{index + 1}}</td>
                    <td @click="setChatId(post.clientId)">{{post.clientId}}</td>
                    <td>{{decodeURIComponent(post.name)}}</td>
                    <td>{{post.x}}</td>
                    <td>{{post.y}}</td>
                    <td>{{post.fat}}</td>
                    <td>{{post.needSleep}}</td>
                    <td>{{getActionNameById(post.currentAction)}}</td>
                </tr>
            </table>

        </div>

        <div v-if="scores && scores.message">
            {{scores.message}}
        </div>

        <button class="beaty" @click="getScore()">Включть обновление</button>
        <button class="beaty" @click="cancelAutoUpdate()">Отменить обновление</button>

        <div v-if="errors && errors.length">
            <div class="scrollable">
                <ul>
                    <li v-for="error in errors">
                        {{error.message.replace("\n", "&lt;br&gt;")}}
                    </li>
                </ul>
            </div>

            <button @click="errors = []">Удалить сообщения об ошибках</button>
        </div>

    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        name: 'UserList',
        data () {
            return {
                errors: [],
                scores : {},
                timer: ''
            }
        },
        props: {
            serverHost : 'localhost',
            posts: []
        },
        created() {
//            axios.get(`http://` + this.serverHost + `:8090/rest/rabbit/list`)
//                .then(response => {
//                    // JSON responses are automatically parsed.
//                    this.posts = response.data
//                })
//                .catch(e => {
//                    this.errors.push(e)
//                });
//            this.timer = setInterval(this.getScore, 1000);
        },
        methods: {
            getScore: function () {
                axios.get(`http://` + this.serverHost + `:8090/rest/rabbit/list`)
                    .then(response => {
                        // JSON responses are automatically parsed.
                        this.posts = response.data
                    })
                    .catch(e => {
                        this.errors.push(e)
                    })

                if (!this.timer) this.timer = setInterval(this.getScore, 2000);
            },
            cancelAutoUpdate: function() {
                window.clearInterval(this.timer);
                this.timer = '';
            },
            getActionNameById: function (id) {
                let actionName = '';
                switch (id){
                    case 0 : actionName = 'Непонятно'; break;
                    case 1 : actionName = 'Ест'; break;
                    case 2 : actionName = 'Идёт'; break;
                    case 3 : actionName = 'Спит'; break;
                    case 4 : actionName = 'Отдыхает'; break;
                    default: actionName = 'Непонятно';
                }
                return actionName;
            },
            setChatId: function (id) {
                this.$emit("setChatId", id);
            }
        },
        computed: {
            orderedUsers: function () {
                return this.posts.sort((a, b) => a.eatedGrass > b.eatedGrass ? -1 : 1);
            }
        },
        beforeDestroy() {
            window.clearInterval(this.timer)
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    table {
        font-family: "Lucida Sans Unicode", "Lucida Grande", Sans-Serif;
        font-size: 14px;
        border-radius: 10px;
        border-spacing: 0;
        text-align: center;
        align: center;
    }
    th {
        background: #BCEBDD;
        color: white;
        text-shadow: 0 1px 1px #2D2020;
        padding: 4px 6px;
    }
    th, td {
        border-style: solid;
        border-width: 0 1px 1px 0;
        border-color: white;
    }
    th:first-child, td:first-child {
        text-align: left;
    }
    th:first-child {
        border-top-left-radius: 10px;
    }
    th:last-child {
        border-top-right-radius: 10px;
        border-right: none;
    }
    td {
        padding: 4px 6px;
        background: #F8E391;
    }
    tr:last-child td:first-child {
        border-radius: 0 0 0 10px;
    }
    tr:last-child td:last-child {
        border-radius: 0 0 10px 0;
    }
    tr td:last-child {
        border-right: none;
    }

    button.beaty {
        display: inline-block;
        color: black;
        font-size: 125%;
        font-weight: 700;
        text-decoration: none;
        user-select: none;
        padding: .25em .5em;
        outline: none;
        border: 1px solid rgb(250,172,17);
        border-radius: 7px;
        background: rgb(255,212,3) linear-gradient(rgb(255,212,3), rgb(248,157,23));
        box-shadow: inset 0 -2px 1px rgba(0,0,0,0), inset 0 1px 2px rgba(0,0,0,0), inset 0 0 0 60px rgba(255,255,0,0);
        transition: box-shadow .2s, border-color .2s;
    }
    button.beaty:hover {
        box-shadow: inset 0 -1px 1px rgba(0,0,0,0), inset 0 1px 2px rgba(0,0,0,0), inset 0 0 0 60px rgba(255,255,0,.5);
    }
    button.beaty:active {
        padding: calc(.25em + 1px) .5em calc(.25em - 1px);
        border-color: rgba(177,159,0,1);
        box-shadow: inset 0 -1px 1px rgba(0,0,0,.1), inset 0 1px 2px rgba(0,0,0,.3), inset 0 0 0 60px rgba(255,255,0,.45);
    }
    .scrollable {
        height: 200px; /* высота нашего блока */
        border: 1px solid #C1C1C1; /* размер и цвет границы блока */
        overflow-y: scroll; /* прокрутка по вертикали */
    }
</style>
