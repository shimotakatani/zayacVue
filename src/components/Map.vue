<template>
    <div class="map">
        <table>
            <tr v-for="row in cells">
                <td v-for="cell in row">
                    <div v-if="isDrawRabbit(cell)">
                        <div class="cell-background-color__yellow" style="background-color: yellow; min-width: 10px; min-height: 10px"></div>
                    </div>
                    <div v-if="!isDrawRabbit(cell)">
                        <div v-if="isDrawWhiteCell(cell)">
                            <div class="cell-background-color__red" style="background-color: red; min-width: 10px; min-height: 10px"></div>
                        </div>
                        <div v-if="isDrawGreenCell(cell)">
                            <div class="cell-background-color__green" style="background-color: green; min-width: 10px; min-height: 10px"></div>
                        </div>
                        <div v-if="isDrawWallCell(cell)">
                            <div class="cell-background-color__black" style="background-color: black; min-width: 10px; min-height: 10px"></div>
                        </div>
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
                rabbits: []
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
                                    cells[currentRabbit.x + halfCapacity - item.x][currentRabbit.y + halfCapacity - item.y].hasRabbit = true;
                                }
                            }
                        }
                    });

                    this.cells = cells;
                    console.log(this.cells);
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
        },
        watch: {
            map: function(val){
                this.loadMap(val, this.rabbits);
            }
        }
    }
</script>

<style scoped>
    div.cell-background-color__red {
        background-color: red;
        min-width: 10px;
        min-height: 10px
    }
    table {
        border: 0;
        margin: 0;
        padding: 0;
        border-spacing: 0;
    }
    tr {
        border: 0;
        margin: 0;
        padding: 0;
    }
    td {
        border: 0;
        margin: 0;
        padding: 0;
    }
    div.cell-background-color__green {
        background-color: green;
        min-width: 10px;
        min-height: 10px
    }
    div.cell-background-color__yellow {
        background-color: yellow;
        min-width: 10px;
        min-height: 10px
    }
    div.cell-background-color__black {
        background-color: black;
        min-width: 10px;
        min-height: 10px
    }
</style>
