<template>
  <div class="little_map">
      <canvas id="littleMap"></canvas>
  </div>
</template>

<script>
    export default {
        name: 'LittleMap',
        props: {
            msg: '',
            capacity : 0
        },
        methods: {
            fillMap: function (msg, capacity) {
                let canvas = document.getElementById("littleMap"),
                    ctx     = canvas.getContext('2d');
                ctx.canvas.height = 200;
                ctx.canvas.width = 200;

                if (msg && msg.length && capacity){
                    for(let i = 0; i < capacity; i++){
                        for(let j = 0; j < capacity; j++){
                            ctx.fillStyle = this.getColorByCode(msg[ i * capacity + j]); // меняем цвет клеток
                            ctx.fillRect(i, j, 1, 1);
                        }
                    }
                }
            },
            getColorByCode: function (code) {
                switch (code) {
                    case '0': return '#FFA500';
                    case '1': return '#50FA0F';
                    case '3': return '#FAFAFA';
                    case '4': return '#000000';
                }
            }
        },
        watch:{
            msg : function (value) {
                this.fillMap(value, this.capacity);
                return value;
            }
        }
    }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
    canvas {
        width: 600px;
        height: 600px;
    }
</style>
