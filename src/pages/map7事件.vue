<template>
    <div class="ol-map" ref="olMap"></div>
</template>

<script>
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
import VectorLayer from "ol/layer/Vector";
import XYZ from "ol/source/XYZ";
// import OSM from "ol/source/OSM";
import { fromLonLat } from "ol/proj"; //将坐标从经度/纬度转换为其他投影。
import { defaults as defaultInteractions, DragRotateAndZoom } from "ol/interaction";
import Feature from "ol/Feature";
import SourceVector from "ol/source/Vector";
import { Circle as gCircle, LineString, Point } from "ol/geom";
import { Style, Circle, Fill, Stroke } from "ol/style";
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
                // ol.source.XYZ，这个需要单独提一下，因为是可以直接使用的，而且现在很多地图服务（在线的，或者自己搭建的服务器）都支持xyz方式的请求。国内在线的地图服务，高德、天地图等，都可以通过这种方式加载，本地离线瓦片地图也可以，用途广泛，且简单易学。
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
                }),
                target: this.$refs.olMap, // DOM容器
                interactions: defaultInteractions().extend([new DragRotateAndZoom()]), //控制地图的旋转
            });
            // 几乎OpenLayers 3中所有的类，都能监听事件和触发事件，
            // 因为它们都继承于类ol.Observable，这个类甚至是ol.Object的父类。
            // 响应函数listener具有一个参数event，这个event类就对应于API文档中事件名称后边括号里的类。
            // this.map.on("click", (event) => {
            //     console.log("地图点击事件");
            //     console.log(event);
            // });

            // 解除绑定
            // let dbClick = (event) => {
            //     console.log("地图双击事件");
            //     console.log(event);
            //     this.map.un("dblclick", dbClick);
            // };
            // this.map.on("dblclick", dbClick);

            //只执行一次
            // this.map.once("click", (event) => {
            //     console.log("地图点击事件");
            //     console.log(event);
            // });

            let pointFeature = new Feature({
                geometry: new Point([12958998, 4852221]),
            });
            pointFeature.setStyle(
                new Style({
                    image: new Circle({
                        radius: 100,
                        fill: new Fill({
                            color: "red",
                        }),
                    }),
                })
            );
            let vectorLayer = new VectorLayer({
                source: new SourceVector({
                    features: [pointFeature],
                }),
            });
            this.map.addLayer(vectorLayer);

            this.map.on("click", (event) => {
                this.map.forEachFeatureAtPixel(event.pixel, (feature) => {
                    // console.log(feature);
                    // 给要素派发自定义事件
                    // feature.dispatchEvent({ type: "mouseClick", event: event });
                    feature.dispatchEvent({ type: "mouseClick", event: { id: "哈哈" } });
                });
            });

            // 绑定自定义事件
            pointFeature.on("mouseClick", (event) => {
                console.log(event);
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
