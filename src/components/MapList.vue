<template>
    <div class="hello">
        <div v-if="maps && maps.length" style="display: list-item">
            <table align="center">
                <tr>
                    <th>#</th>
                    <th>id</th>
                    <th>capacity</th>
                </tr>
                <tr v-for="(map, index) in maps">
                    <td>{{index + 1}}</td>
                    <td @click="setMapId(map.id)">{{map.id}}</td>
                    <td>{{map.capacity}}</td>
                </tr>
            </table>
        </div>
    </div>
</template>

<script>
    export default {
        name: "MapList",
        data () {
            return {
            }
        },
        props: {
            maps: []
        },
        created() {

        },
        methods: {
            setMapId: function (id) {
                this.$emit("setMapId", id);
            }
        }
    }
</script>

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