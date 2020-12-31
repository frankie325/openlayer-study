<template>
    <div class="ol-map" ref="olMap"></div>
</template>

<script>
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
// import XYZ from "ol/source/XYZ";
import XYZ from "ol/source/XYZ";
import { fromLonLat } from "ol/proj";
import { defaults as defaultInteractions, DragRotateAndZoom } from "ol/interaction";
import { defaults, Attribution, FullScreen, MousePosition, OverviewMap, Rotate, ScaleLine, ZoomSlider, ZoomToExtent, Zoom } from "ol/control";
export default {
    name: "",
    data() {
        return {
            map: null,
        };
    },
    methods: {
        getMap() {
            const tileLayer = new TileLayer({
                source: new XYZ({
                    url: "https://webrd01.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=8&x={x}&y={y}&z={z}",
                }),
            });
            this.map = new Map({
                layers: [tileLayer],
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
                /*
                    OpenLayers实现了一个对原生JavaScript的Array类进行扩展的类ol.Collection，包含Array的所有操作
                    Map对象会保存一个ol.Collection实例用于存放控件。
                    下面的新增代码中，ol.control.defaults()方法就用于返回保存默认控件的ol.Collection实例，
                    然后使用ol.Collection类的extend()方法往里增加了滑块缩放控件。
                */
                controls: defaults().extend([
                    new Attribution(), //用于展示地图资源的版权或者归属，它会默认加入到地图中。
                    new FullScreen(), //全屏按钮
                    new MousePosition(), //显示鼠标当前的坐标
                    new OverviewMap(), //生成地图的一个概览图
                    new Rotate(), //按钮控件，用于将旋转重置为0
                    new ScaleLine(), //比例尺控件
                    new ZoomSlider(), //  以滑块的形式缩放地图控件
                    new ZoomToExtent({
                        // 缩放至特定位置的按钮控件
                        extent: [12667718, 2562800, 12718359, 2597725],
                    }),
                    new Zoom(), //普通缩放控件，它会默认加入到地图中
                ]),
            });
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
