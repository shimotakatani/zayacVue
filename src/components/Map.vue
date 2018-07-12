<template>
  <div class="map">
    <table>
        <tr v-for="row in cells">
          <td v-for="cell in row">
              <div v-if="isDrawWhiteCell(cell)">
                  <div class="cell-background-color__white" style="background-color: white; min-width: 10px; min-height: 10px"></div>
              </div>
              <div v-if="isDrawGreenCell(cell)">
                  <div class="cell-background-color__green" style="background-color: green; min-width: 10px; min-height: 10px"></div>
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
            cells : [[]]
        }
    },
  props: {
    map : null
  },
    methods:{
      loadMap: function(map){
          if (map) {
              let capacity = map.capacity;
              let cells = [];
              for(let i=0; i < capacity; i++){
                  cells[i] = [];
              }
              map.cells.forEach(function (item) {
                  cells[item.x][item.y] = item;
              });
              this.cells = cells;
              console.log(this.cells);
          }
      },
        isDrawWhiteCell: function (cell) {
            return cell.plant == 0;
        },
        isDrawGreenCell: function (cell) {
            return cell.plant == 1;
        }
    },
    watch: {
      map: function(val){
        this.loadMap(val);
      }
    }
}
</script>

<style scoped>
    div.cell-background-color__white {
        background-color: white;
        min-width: 10px;
        min-height: 10px
    }
    table {
        border: 20px black;
    }
    div.cell-background-color__green {
        background-color: green;
        min-width: 10px;
        min-height: 10px
    }
</style>
