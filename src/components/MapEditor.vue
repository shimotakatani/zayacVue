<template>
    <div>
        <table>
            <tr>
                <td>
                    <map-list :maps="maps" v-on:setMapId="selectMap"></map-list>
                </td>
                <td>
                    <big-map :currentMap="currentMap"></big-map>
                </td>
            </tr>
        </table>
        <p>
            {{JSON.stringify(currentMap)}}
        </p>
        <p>
            {{JSON.stringify(log)}}
        </p>
    </div>

</template>

<script>
    import MapList from "./MapList";
    import BigMap from "./BigMap";
    export default {
        name: "MapEditor",
        components: {BigMap, MapList},
        data: function () {
            return {
                maps: [],
                currentMap: {},
                cashMap: [],
                log: {}
            }
        },
        props: {
            instance: {}
        },
        created: function () {
            setTimeout(this.loadMaps(), 2000);
        },
        methods: {
            loadMaps: function () {
                let _this = this;
                if (this.instance && this.instance.get) {
                    this.instance.get('/rest/map/list').then((response) => {
                        if (response) {
                            console.log(response.data);
                            _this.maps = response.data;
                        }
                    });
                }
            },
            loadMapById: function (id) {
                if (this.instance && this.instance.get) {
                    this.loadMapCadr(id, 0);
                }
            },
            loadMapCadr: function (id, cadrNumber) {
                let _this = this;
                let finalCadr = false;
                this.instance.get(`/rest/map?id=${id}&cadr=${cadrNumber}`).then((response) => {
                    if (response && response.data) {
                        _this.cashMap[cadrNumber] = response.data.mapString;
                        finalCadr = response.data.finalCadr;
                        cadrNumber++;
                    } else {
                        finalCadr = true;
                    }
                    if (!finalCadr) {
                        this.loadMapCadr(id, cadrNumber);
                    } else {
                        _this.currentMap = {
                            mapString: _this.cashMap.join(''),
                            capacity: response.data.capacity
                        };
                    }
                });

            },
            selectMap: function (id) {
                if (id || id === 0) {
                    this.loadMapById(id);
                }
            }
        }
    }
</script>

<style scoped>

</style>