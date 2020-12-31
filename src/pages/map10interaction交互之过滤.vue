<template>
    <div>
        <div class="ol-map" tabindex="1" ref="olMap"></div>
        <!-- 需要设置tabindex，才能使div获得键盘事件 -->
        <!-- <div ref="overDiv"><img src="../assets/logo.png" alt="" srcset="" /></div> -->
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
import { Select } from "ol/interaction";
import { Style, Circle, Fill, RegularShape } from "ol/style";
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

            let circle1 = new Feature({
                geometry: new Point(fromLonLat([120.771441, 30.756433])),
            });

            let circle2 = new Feature({
                geometry: new Point(fromLonLat([110.771441, 30.756433])),
            });

            const vectorLayer = new VectorLayer({
                source: new VectorSource({
                    features: [circle1, circle2],
                }),
                style: new Style({
                    image: new Circle({
                        radius: 10,
                        fill: new Fill({
                            color: "red",
                        }),
                    }),
                }),
            });

            this.map.addLayer(vectorLayer);

            let selectSingleClick = new Select({
                /*
                    ol.events.condition里面有很多内置的条件过滤函数，可以在官网API查询
                */
                // 添加一个用于选择Feature的交互方式
                condition: pointerMove, //设置鼠标移到feature上就选取
                style: new Style({
                    image: new Circle({
                        radius: 100,
                        fill: new Fill({
                            color: "blue",
                        }),
                    }),
                }),
                // 关键： 设置过滤条件，可以用feature来写过滤，也可以用layer来写过滤
                // filter: (feature) => {
                //     return feature === circle2; //返回的是不过滤的
                // },
                multi: true, //能同时选择多个
            });
            this.map.addInteraction(selectSingleClick);

            // 监听选中事件，然后在事件处理函数中操作选中的`feature`
            selectSingleClick.on("select", (event) => {
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
