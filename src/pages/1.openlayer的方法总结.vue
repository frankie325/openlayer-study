<template> </template>

<script>
let map = new Map();
// 1.获取所有的图层
map.getLayers().getArray();

// 2.判断坐标是否在几何图形内
layer
    .getSource() //获取图层的数据源
    .getFeatures()[0] //获取数据源中的所有要素
    .getGeometry() //获取要素的几何图形类
    .intersectsCoordinate([95.00112173133901, 43.701171327094116]); //调用几何图形类中的方法，该方法判断坐标是否在几何图形内

// 3.获取每单位有多少米("EPSG:4326")，可用来计算两坐标点之间的距离
map.getView()
    .getProjection()
    .getMetersPerUnit();
function getDistance(coordinate) {
    let lng = parseFloat(coordinate[0]);
    let lat = parseFloat(coordinate[1]);
    let blng = parseFloat(this.anlAddress.split(",")[0]);
    let blat = parseFloat(this.anlAddress.split(",")[1]);
    let metersPerUnit = map
        .getView()
        .getProjection()
        .getMetersPerUnit();
    let distance = Math.sqrt(Math.pow(blng - lng, 2) + Math.pow(blat - lat, 2)) * metersPerUnit;
    return Number(distance).toFixed(2);
}

// 4.forEachFeatureAtPixel方法,针对矢量数据源，通过鼠标点击坐标与map坐标对比，获取选中要素
this.map.on("click", (event) => {
    //如果点击到了要素，就触发回调
    this.map.forEachFeatureAtPixel(event.pixel, (feature) => {
        console.log(feature);
    });
});

// 5.ol.proj.fromLonLat(coordinate, opt_projection)
// 将坐标从经度/纬度转换为其他投影,opt_projection为目标投影，默认为EPSG:3857
</script>

<style lang="" scoped></style>
