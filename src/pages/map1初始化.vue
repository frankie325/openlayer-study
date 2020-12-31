<template>
    <div class="ol-map" ref="olMap"></div>
</template>

<script>
import Map from "ol/Map";
import View from "ol/View";
import TileLayer from "ol/layer/Tile";
import XYZ from "ol/source/XYZ";
// import OSM from "ol/source/OSM";
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
        // https://blog.csdn.net/qq_35732147/category_7819503.html   openlayers教程
        // http://linwei.xyz/ol3-primer/ch09/09-01.html  openlayers教程
        getMap() {
            // 使用高德
            const tileLayer = new TileLayer({
                // ol.source.XYZ，这个需要单独提一下，因为是可以直接使用的，而且现在很多地图服务（在线的，或者自己搭建的服务器）都支持xyz方式的请求。国内在线的地图服务，高德、天地图等，都可以通过这种方式加载，本地离线瓦片地图也可以，用途广泛，且简单易学。
                source: new XYZ({
                    url: "https://webrd01.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=8&x={x}&y={y}&z={z}",
                }),
                // source: new OSM(), // OpenStreetMap数据源
            });
            /*把整个地图看作一个容器（Map），核心为地图图层（Layer），每个图层有对应的数据源（Source），
            并由地图视图（View）进行地图表现。地图容器上还支持一些与用户交互的控件（Control和Interaction），
            另外，OpenLayers还支持事件机制*/
            /*首先，调用了ol.Map类生成了一个地图容器对象，通过target参数关联到div容器（id为"map"的div元素）。
            然后通过layers参数设置需要加载的瓦片图层（ol.layer.Tile），这个瓦片图层中包含了一个数据源（ol.source.OSM），这个数据源是OpenStreetMap（一个开放数据源的免费地图）的地图数据，也就是ol.source.OSM这个类封装了加载OSM地图数据的详细实现。
            最后通过view参数设置地图视图（ol.View），地图视图中也设置了相应的空间参考系统（projection）、地图视图中心点（center），地图视图缩放级别（zoom）。*/
            this.map = new Map({
                layers: [tileLayer], //瓦片图层
                view: new View({
                    //目前OpenLayers 3支持两种投影，一个是EPSG:4326,以经纬度为坐标，另一个是EPSG:3857，以米为单位
                    projection: "EPSG:3857", //空间参考系统，墨卡托投影坐标系 //使用这个坐标系，默认为 3857,可以不写
                    center: fromLonLat([120.771441, 30.756433]), //地图中心点,fromLonLat将坐标从经度/纬度转换为其他投影坐标
                    zoom: 15, // 缩放级别
                    minZoom: 0, // 最小缩放级别
                    maxZoom: 18, // 最大缩放级别
                    constrainResolution: true, // 因为存在非整数的缩放级别，所以设置该参数为true来让每次缩放结束后自动缩放到距离最近的一个整数级别，这个必须要设置，当缩放在非整数级别时地图会糊
                }),
                target: this.$refs.olMap, // DOM容器
                interactions: defaultInteractions().extend([new DragRotateAndZoom()]), //控制地图的旋转
            });
            console.log(this.map);
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
