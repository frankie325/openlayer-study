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
import { defaults, MousePosition } from "ol/control";

import Feature from "ol/Feature";
import { Point, LineString, Polygon, Circle } from "ol/geom";
import SourceVector from "ol/source/Vector"; //ol.source.Vector是矢量数据源基类，为矢量图层ol.layer.Vector类提供具体的数据来
import { Style, Stroke, Fill } from "ol/style";
import { transform } from "ol/proj";
export default {
    name: "",
    data() {
        return {
            map: null,
        };
    },
    methods: {
        // 添加矢量图层
        /*
        前面介绍的瓦片地图将地理信息以一块块瓦片的形式进行组织并渲染，瓦片的本质是图片，
        因此不能对瓦片地图进行修改样式、空间分析等操作，而且瓦片不包含属性信息，隐含的空间信息也不能直接获取使用。
        矢量数据使用矢量数据模型来组织地理信息，矢量数据模型采用离散对象来表示地球表面的空间要素，
        因此，简单来说，矢量数据包含了各个地理要素的空间坐标与属性信息，这使我们能对地理信息进行细粒度的使用与操作。
        */
        getMap() {
            //  在OpenLayers中，使用ol.Feature类表示地理要素，一个Feature对象就表示一个地理要素。
            //  Feature对象的空间信息分别通过ol.geom.Point类、ol.geom.LineString类，赋值给geometry参数
            // 初始化一个点要素
            let pointFeature = new Feature({
                geometry: new Point([12958998, 4852221]), // 空间信息
                name: "点要素", // 属性信息
            });

            // 初始化一个线要素
            let lineFeature = new Feature({
                // 空间信息
                geometry: new LineString([
                    [11590147, 4322577],
                    [13594369, 3872784],
                ]),
                name: "线要素", // 属性信息
            });

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

            //如果地图层坐标系为EPSG:4326，要以米为半径画圆
            // 先将坐标转化为3857
            // let circle3857 = new gCircle(transform([lng, lat], "EPSG:4326", "EPSG:3857"), radius);
            //再将圆的坐标从3857坐标参考系统转换为4326坐标参考系统
            // let circle4326 = circle3857.transform("EPSG:3857", "EPSG:4326");

            // 初始化一个圆要素
            let circleFeature = new Feature({
                //矢量图层用的是EPSG:4326坐标系，将经纬度转化为EPSG:3857坐标
                geometry: new Circle(transform([116.406568, 39.916263], "EPSG:4326", "EPSG:3857"), 10000),
            });

            // 初始化一个矢量数据源, 并添加上面创建的要素
            let vectorSource = new SourceVector();
            vectorSource.addFeature(pointFeature);
            vectorSource.addFeature(lineFeature);
            vectorSource.addFeature(polygonFeature);
            vectorSource.addFeature(circleFeature);

            // 初始化一个矢量图层
            let vectorLayer = new VectorLayer({
                source: vectorSource,
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
                    center: fromLonLat([116.406568, 39.916263]),
                    zoom: 15,
                    minZoom: 0,
                    maxZoom: 18,
                    constrainResolution: true,
                }),
                target: this.$refs.olMap,
                interactions: defaultInteractions().extend([new DragRotateAndZoom()]),
                controls: defaults().extend([
                    new MousePosition({
                        projection: "EPSG:3857",
                    }), //显示鼠标当前的坐标
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
}
</style>
