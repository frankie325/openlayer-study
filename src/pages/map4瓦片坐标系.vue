<template>
    <div class="ol-map" ref="olMap"></div>
</template>

<script>
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
import XYZ from "ol/source/XYZ";
import { OSM, TileDebug } from "ol/source";
import { fromLonLat } from "ol/proj"; //将坐标从经度/纬度转换为其他投影。
import { defaults as defaultInteractions, DragRotateAndZoom } from "ol/interaction";
export default {
    name: "",
    data() {
        return {
            map: null,
        };
    },
    methods: {
        getMap() {
            const tileDebug = new TileDebug({
                projection: "EPSG:3857",
                tileGrid: new OSM().getTileGrid(), // 获取OSM地图的瓦片坐标系
            });
            const layer1 = new TileLayer({
                source: new OSM(),
            });
            const layer2 = new TileLayer({
                source: tileDebug,
            });
            this.map = new Map({
                layers: [layer1, layer2],
                view: new View({
                    projection: "EPSG:3857",
                    center: fromLonLat([120.771441, 30.756433]),
                    zoom: 15,
                    minZoom: 0,
                    maxZoom: 18,
                    constrainResolution: true,
                }),
                target: this.$refs.olMap,
                interactions: defaultInteractions().extend([new DragRotateAndZoom()]),
            });
        },
    },
    mounted() {
        this.getMap();
    },
};
</script>

<style scoped>
.ol-map {
    width: 100%;
    height: 800px;
}
</style>
