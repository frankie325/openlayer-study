<template>
    <div>
        <div class="ol-map" ref="olMap"></div>
    </div>
</template>

<script>
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
import VectorLayer from "ol/layer/Vector";
import XYZ from "ol/source/XYZ";
import VectorSource from "ol/source/Vector";
// import OSM from "ol/source/OSM";
import { fromLonLat } from "ol/proj"; //将坐标从经度/纬度转换为其他投影。
import { defaults as defaultInteractions, DragRotateAndZoom } from "ol/interaction";
import Feature from "ol/Feature";
import { Point, Polygon } from "ol/geom";
import { Select, Draw } from "ol/interaction";
import { Style, Circle, Fill, RegularShape, Stroke } from "ol/style";
import { pointerMove } from "ol/events/condition";
export default {
    name: "",
    data() {
        return {
            map: null,
        };
    },
    methods: {
        getMap() {
            // 使用高德
            const tileLayer = new TileLayer({
                source: new XYZ({
                    url: "https://webrd01.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=8&x={x}&y={y}&z={z}",
                }),
                // source: new OSM(), // OpenStreetMap数据源
            });

            this.map = new Map({
                layers: [tileLayer], //瓦片图层
                view: new View({
                    projection: "EPSG:3857",
                    center: fromLonLat([120.771441, 30.756433]),
                    zoom: 1,
                    minZoom: 0,
                    maxZoom: 18,
                    constrainResolution: true,
                }),
                target: this.$refs.olMap, // DOM容器
                interactions: defaultInteractions({
                    doubleClickZoom: true, //双击缩放
                    mouseWheelZoom: true, //鼠标滚动缩放
                    shiftDragZoom: true, //shift加鼠标选择区域缩放
                    pinchZoom: true, //触屏时，用手指进行缩放
                }),
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
