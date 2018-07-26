<template>
    <div class="map">
        <span>Размер клетки поля: </span><input v-model="cellSize" v-on:change="setRadius(cellSize)">
        <table>
            <tr v-for="row in cells">
                <td v-for="cell in row">
                    <div v-if="isDrawWhiteCell(cell)">
                        <div class="cell-background-color__red" v-bind:style="radiusObject" >
                            <div v-if="isDrawRabbit(cell)">
                                <img v-bind:style="radiusObject" src="rabbit2.png">
                            </div>
                        </div>
                    </div>
                    <div v-if="isDrawGreenCell(cell)">
                        <div class="cell-background-color__green" v-bind:style="radiusObject">
                            <div v-if="isDrawRabbit(cell)">
                                <img v-bind:style="radiusObject" src="rabbit2.png">
                            </div>
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
        name: 'Map',
        data: function () {
            return {
                cells : [[]],
                rabbits: [],
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
            map : null,
            rabbits: null,
            chatId: 0
        },
        methods:{
            loadMap: function(map, rabbits){
                if (map) {
                    let capacity = map.capacity;
                    let cells = [];
                    for(let i=0; i < capacity; i++){
                        cells[i] = [];
                    }
                    map.cells.forEach(function (item) {
                        cells[item.x][item.y] = item;
                    });

                    let currentRabbit = null;
                    rabbits.forEach((item) =>{
                        if (item.clientId == this.chatId) {
                            currentRabbit = item;
                        }
                    });

                    let halfCapacity = Math.floor(capacity / 2);

                    cells[halfCapacity][halfCapacity].hasRabbit = true;

                    rabbits.forEach((item) =>{
                        if (item.clientId != this.chatId) {
                            if ((item.x >= (currentRabbit.x - halfCapacity)) && (item.x <= (currentRabbit.x + halfCapacity))) {
                                if ((item.y >= (currentRabbit.y - halfCapacity)) && (item.y <= (currentRabbit.y + halfCapacity))) {
                                    cells[item.x + halfCapacity - currentRabbit.x][item.y + halfCapacity - currentRabbit.y].hasRabbit = true;
                                }
                            }
                        }
                        //cells[item.x][item.y].hasRabbit = true;
                    });

                    this.cells = cells;
                }
            },
            isDrawWhiteCell: function (cell) {
                return cell.plant == 0 && cell.ground == 0;
            },
            isDrawWallCell: function (cell) {
                return cell.plant == 0 && cell.ground == 4;
            },
            isDrawGreenCell: function (cell) {
                return cell.plant == 1;
            },
            isDrawRabbit: function (cell) {
                return cell.hasRabbit == true;
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
            map: function(val){
                this.loadMap(val, this.rabbits);
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
