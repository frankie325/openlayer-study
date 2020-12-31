<template>
    <div class="ol-map" ref="olMap"></div>
</template>

<script>
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
import VectorLayer from "ol/layer/Vector";
// import XYZ from "ol/source/XYZ";
import XYZ from "ol/source/XYZ";
import { fromLonLat } from "ol/proj";
import { defaults as defaultInteractions, DragRotateAndZoom } from "ol/interaction";

import Feature from "ol/Feature";
import { Point, LineString, Polygon } from "ol/geom";
import SourceVector from "ol/source/Vector"; //ol.source.Vector是矢量数据源基类，为矢量图层ol.layer.Vector类提供具体的数据来
import { Style, Stroke, Fill, Circle, Icon } from "ol/style";
export default {
    name: "",
    data() {
        return {
            map: null,
        };
    },
    methods: {
        // https://blog.csdn.net/qingyafan/article/details/46273663 样式
        /*
        对矢量地图进行样式设置，OpenLayers支持两种方式，一种是直接给feature设置样式，
        一种是给layer设置样式。系统默认优先考虑feature的样式，如果没有，
        则使用layer的样式，还有一种情况是layer也没有设置样式，则会采用系统默认的样式。
        */
        getMap() {
            let pointFeature = new Feature({
                geometry: new Point([12958998, 4852221]),
            });
            // 点要素设置样式需要在image下设置，也可以设置图片
            pointFeature.setStyle(
                // 这样设置的点，大小不会随着地图的缩放改变
                // new Style({
                //     image: new Circle({
                //         radius: 10,
                //         fill: new Fill({
                //             color: "red",
                //         }),
                //         stroke: new Stroke({
                //             color: "yellow",
                //             width: 5,
                //         }),
                //     }),
                // })
                new Style({
                    image: new Icon({
                        src: require("../assets/logo.png"),
                        scale: 0.12,
                    }),
                })
            );
            // 初始化一个线要素
            let lineFeature = new Feature({
                // 空间信息
                geometry: new LineString([
                    [11590147, 4322577],
                    [13594369, 3872784],
                ]),
                name: "线要素", // 属性信息
            });
            lineFeature.setStyle(
                new Style({
                    stroke: new Stroke({
                        color: "red",
                        width: 5,
                    }),
                })
            );
            // 初始化一个多边形要素
            let polygonFeature = new Feature({
                // 空间信息
                geometry: new Polygon([
                    [
                        [11801814, 3251012],
                        [14057391, 2748303],
                        [12714628, 1346008],
                        [11801814, 3251012],
                    ],
                ]),
                name: "多边形要素", // 属性信息
            });

            polygonFeature.setStyle(
                new Style({
                    stroke: new Stroke({
                        color: "blue",
                        width: 5,
                    }),
                })
            );

            // 初始化一个矢量数据源, 并添加上面创建的要素
            let vectorSource = new SourceVector();
            vectorSource.addFeature(pointFeature);
            vectorSource.addFeature(lineFeature);
            vectorSource.addFeature(polygonFeature);

            // 初始化一个矢量图层
            let vectorLayer = new VectorLayer({
                source: vectorSource,
                //为所有feature添加样式
                // style: new Style({
                //     // 描边样式
                //     stroke: new Stroke({
                //         color: "red",
                //         width: 5,
                //     }),
                //     // 填充样式
                //     fill: new Fill({
                //         color: "green",
                //     }),
                // }),
            });

            const tileLayer = new TileLayer({
                source: new XYZ({
                    url: "https://webrd01.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=8&x={x}&y={y}&z={z}",
                }),
            });
            this.map = new Map({
                layers: [tileLayer, vectorLayer],
                view: new View({
                    projection: "EPSG:3857",
                    center: fromLonLat([120.771441, 30.756433]),
                    zoom: 1,
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

<style lang="less" scoped>
.ol-map {
    width: 100%;
    height: 800px;
}
</style>
