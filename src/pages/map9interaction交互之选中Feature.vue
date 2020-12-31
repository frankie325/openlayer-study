<template>
    <div>
        <div class="ol-map" ref="olMap"></div>
        <button @click="cancelSelect">取消选中</button>
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
import { Point } from "ol/geom";
import { Select } from "ol/interaction";
import { Style, Circle, Fill } from "ol/style";

export default {
    name: "",
    data() {
        return {
            map: null,
            selectSingleClick: null,
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
                /*
                    OpenLayers 3除了在地图浏览方面提供内置的交互方式之外，还提供了地图上Feature选取的交互类：
                    ol.interaction.Select。 这是一个经常会用到的类，应用范围非常的广。
                */
                interactions: defaultInteractions({
                    doubleClickZoom: true, //双击缩放
                    mouseWheelZoom: true, //鼠标滚动缩放
                    shiftDragZoom: true, //shift加鼠标选择区域缩放
                    pinchZoom: true, //触屏时，用手指进行缩放
                }),
            });

            const vectorLayer = new VectorLayer({
                source: new VectorSource(),
                // style: new Style({
                //     image: new Circle({
                //         radius: 10,
                //         fill: new Fill({
                //             color: "red",
                //         }),
                //     }),
                // }),
            });

            let circle = new Feature({
                geometry: new Point(fromLonLat([120.771441, 30.756433])),
            });
            circle.setStyle(
                new Style({
                    image: new Circle({
                        radius: 10,
                        fill: new Fill({
                            color: "red",
                        }),
                    }),
                })
            );
            vectorLayer.getSource().addFeature(circle);
            this.map.addLayer(vectorLayer);

            //可以设置style参数，用来设置选中后的样式。但是如果在Feature中设置了样式，此时选中的样式不会改变，因为样式优先级:Feature样式>interaction设置的样式>vectorLayer中样式
            this.selectSingleClick = new Select({
                style: new Style({
                    image: new Circle({
                        radius: 100,
                        fill: new Fill({
                            color: "blue",
                        }),
                    }),
                }),
            });
            this.map.addInteraction(this.selectSingleClick);

            // 监听选中事件，然后在事件处理函数中操作选中的`feature`
            this.selectSingleClick.on("select", (event) => {
                console.log(event);
            });
        },
        cancelSelect() {
            // 取消选中
            // 1.获取选中的Feature，取消掉
            this.selectSingleClick.getFeatures().clear();
            // 2.
            // this.map.removeInteraction(this.selectSingleClick);
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
