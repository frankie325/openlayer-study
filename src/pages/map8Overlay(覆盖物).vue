<template>
    <div>
        <div class="ol-map" ref="olMap"></div>
        <div ref="overDiv"><img src="../assets/logo.png" alt="" srcset="" /></div>
    </div>
</template>

<script>
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
import XYZ from "ol/source/XYZ";
// import OSM from "ol/source/OSM";
import { fromLonLat } from "ol/proj"; //将坐标从经度/纬度转换为其他投影。
import { defaults as defaultInteractions, DragRotateAndZoom } from "ol/interaction";
import Overlay from "ol/Overlay";
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
                    zoom: 1, // 缩放级别
                    minZoom: 0, // 最小缩放级别
                    maxZoom: 18, // 最大缩放级别
                    constrainResolution: true,
                    controls:[

                    ]
                }),
                target: this.$refs.olMap, // DOM容器
                interactions: defaultInteractions().extend([new DragRotateAndZoom()]), //控制地图的旋转
            });

            /*
            Overlay 从名字看，是覆盖图、覆盖物的意思，主要的用途就是在地图之上再覆盖一层，(不是图层)
            用以显示额外的可见元素，可见元素一般是 HTML 元素，利用 overlay，可以将可见元素放置到地图的任意位置，形成地图上再浮动一层的效果。
            */
            let overlayLogo = new Overlay({
                element: this.$refs["overDiv"],
                position: fromLonLat([120.771441, 30.756433]),
            });
            this.map.addOverlay(overlayLogo)
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
