<template>
    <div>
        <button @click="changeMap">切换图层</button>
        <div class="ol-map" ref="olMap"></div>
    </div>
</template>

<script>
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
import OSM from "ol/source/OSM";
import XYZ from "ol/source/XYZ";
import { fromLonLat } from "ol/proj";
import { defaults as defaultInteractions, DragRotateAndZoom } from "ol/interaction";
import { defaults, Attribution, FullScreen, MousePosition, OverviewMap, Rotate, ScaleLine, ZoomSlider, ZoomToExtent, Zoom } from "ol/control";
export default {
    name: "",
    data() {
        return {
            map: null,
            isLayer2: false,
        };
    },
    methods: {
        getMap() {
            const layer1 = new TileLayer({
                source: new XYZ({
                    url: "https://webrd01.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=8&x={x}&y={y}&z={z}",
                }),
            });
            const layer2 = new TileLayer({
                source: new OSM(),
                visible: false, // 先隐藏该图层
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
        // 切换图层
        changeMap() {
            this.isLayer2 = !this.isLayer2;
            //map.getLayers()返回一个ol.Collection类的对象，该对象中包含了地图中的三个图层对象（ol.layer.Tile），
            //可以为item()方法传入对应索引来取出对应图层对象。
            let layers = this.map.getLayers();
            if (this.isLayer2) {
                layers.item(0).setVisible(false); //设置图层的可见性，属于ol/layer/Tile类中的方法
                layers.item(1).setVisible(true);
            } else {
                layers.item(0).setVisible(true);
                layers.item(1).setVisible(false);
            }
            console.log(layers);
            // console.log(layers.item(1));
        },
    },
    mounted() {
        this.getMap();
    },
};
</script>

<style lang="less" scoped>
.ol-map {
    width: 100%;
    height: 800px;
    ::v-deep .ol-zoomslider {
        background-color: rgb(37, 231, 167);
        // height: 200px;
        //   top: 2.3em;
    }
}
</style>
