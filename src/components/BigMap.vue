<template>
    <div class="map">
        <span>Размер клетки поля: </span><input v-model="cellSize" v-on:change="setRadius(cellSize)">
        <table>
            <tr v-for="row in cells">
                <td v-for="cell in row">
                    <div v-if="isDrawWhiteCell(cell)">
                        <div class="cell-background-color__red" v-bind:style="radiusObject" >
                        </div>
                    </div>
                    <div v-if="isDrawGreenCell(cell)">
                        <div class="cell-background-color__green" v-bind:style="radiusObject">
                        </div>
                    </div>
                    <div v-if="isDrawWallCell(cell)">
                        <div class="cell-background-color__black" v-bind:style="radiusObject"></div>
                    </div>
                </td>
            </tr>
        </table>
    </div>
</template>

<script>
    export default {
        name: "BigMap",
        data: function () {
            return {
                cells : [[]],
                cellSize: 60,
                radiusObject: {
                    'min-width': '60px',
                    'min-height': '60px',
                    'max-width': '60px',
                    'max-height': '60px'
                }
            }
        },
        props: {
            currentMap : null
        },
        methods:{
            loadMap: function(map){
                if (map && map.mapString) {
                    let capacity = map.capacity;
                    let cells = [];
                    for(let i=0; i < capacity; i++){
                        cells[i] = [];
                        for (let j=0; j < capacity; j++){
                            cells[i][j] = map.mapString[i*capacity + j];
                        }
                    }

                    this.cells = cells;
                }
            },
            isDrawWhiteCell: function (cell) {
                return cell == 0;
            },
            isDrawWallCell: function (cell) {
                return cell == 4;
            },
            isDrawGreenCell: function (cell) {
                return cell == 1;
            },
            setRadius: function(cellSize){
                if (cellSize && +cellSize){
                    if (cellSize > 4 && cellSize < 100){
                        this.radiusObject = {
                            'min-width': cellSize + 'px',
                            'min-height': cellSize + 'px',
                            'max-width': cellSize + 'px',
                            'max-height': cellSize + 'px'
                        }
                    }
                }
            },
        },
        watch: {
            currentMap: function(val){
                this.loadMap(val);
            }
        }
    }
</script>

<style scoped>

    .radiused {
        min-width: 60px;
        min-height: 60px;
        max-width: 60px;
        max-height: 60px;
    }

    div.cell-background-color__red {
        background-color: red;
        background-image: url(../../public/ground1.png);
    }
    table {
        border-spacing: 0;
        align-self: center;
        margin: auto;
        border: 0;
        padding: 0;
    }

    tr, td, img {
        border: 0;
        margin: 0;
        padding: 0;
    }

    div.cell-background-color__green {
        background-color: green;
        background-image: url(../../public/grass2.png);
    }
    div.cell-background-color__black {
        background-color: black;
        background-image: url(../../public/wall1.png);
    }
</style>
