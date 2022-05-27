---
title: OpenLayers与Cesium视角同步（基于分辨率和视野中心）
date: 2022-01-28 15:54:03
categories:
- 前端
tags:
- OpenLayers
- Cesium
- 同步
- 分辨率
---

网上有很多二三维视角同步的案例，但大多是基于可视四至范围和缩放级别的，其缺陷就是不能准确的还原，三维视角中的地物，在二维视角中的大小和方位关系。

参考ol-cesium库，实现了基于分辨率和视野中心的二三维视角同步（Cesium同步到OpenLayers）。

在旋转、俯仰、平移时，这种同步方式能最大程度的还原三维视角地物在二维视角中的展现。

<!-- more -->

## 代码讲解

``` js
// Cesium的KEY
Cesium.Ion.defaultAccessToken = 'xxx';
// 初始化Cesium的view
const viewer = new Cesium.Viewer('cesiumContainer', {
  terrainProvider: Cesium.createWorldTerrain(),
});
// 初始化OpenLayers的olMap
const olMap = new ol.Map({
  target: 'olContainer',
  view: new ol.View({
    projection: new ol.proj.Projection({
      code: 'EPSG:3857',
      units: 'm',
    }),
    center: [0, 0],
    extent: [-20037508.3427892, -20037508.3427892, 20037508.3427892, 20037508.3427892],
    zoom: 2,
  }),
});
// 添加与Cesium相同的底图
olMap.addLayer(
  new ol.layer.Tile({
    visible: true,
    preload: Infinity,
    source: new ol.source.BingMaps({
      key: 'Aq6cnEx1WH4FDmEW0of-dclQvXRBx3YqpvkWX6_L-NWO2HMbtq6WzhENNYr4G7cZ',
      imagerySet: 'Aerial',
      // use maxZoom 19 to see stretched tiles instead of the BingMaps
      // "no photos at this zoom level" tiles
      // maxZoom: 19
    }),
  })
);
// Cesium绑定事件：视角变化时，执行updateView
viewer.camera.changed.addEventListener(updateView);
viewer.camera.percentageChanged = 0.01;
// Cesium飞到怀柔
viewer.camera.flyTo({
  destination: Cesium.Cartesian3.fromDegrees(115.82, 40.475, 60000),
  orientation: {
    heading: Cesium.Math.toRadians(0.0),
    pitch: Cesium.Math.toRadians(-90.0),
  },
});
// 核心函数，更新视角
function updateView() {
  // 获取Cesium的视野中心作为目标点位
  const ellipsoid = Cesium.Ellipsoid.WGS84;
  const scene = viewer.scene;
  const target = pickCenterPoint(scene);
  // 如果视野中心没获取到，取摄像机所在位置为目标点位
  let bestTarget = target;
  if (!bestTarget) {
    const globe = scene.globe;
    const carto = viewer.camera.positionCartographic.clone();
    const height = globe.getHeight(carto);
    carto.height = height || 0;
    bestTarget = Cesium.Elliposoid.WGS84.cartographicToCartesian(carto);
  }
  // 计算摄像机到目标点位的距离
  const distance = Cesium.Cartesian3.distance(bestTarget, viewer.camera.position);
  // 目标点位作为ol地图的中心
  const bestTargetCartographic = ellipsoid.cartesianToCartographic(bestTarget);
  const view = olMap.getView();
  const center = ol.proj.transform([(bestTargetCartographic.longitude * 180) / Math.PI, (bestTargetCartographic.latitude * 180) / Math.PI], 'EPSG:4326', 'EPSG:3857');
  view.setCenter(center);
  // 计算Cesium的分辨率，设置ol为相同的分辨率
  const resolution = calcResolutionForDistance(distance, bestTargetCartographic ? bestTargetCartographic.latitude : 0, scene, view.getProjection());
  view.setResolution(resolution);
  // 计算Cesium的旋转角度，设置ol的旋转角度
  if (target) {
    const pos = viewer.camera.position;
    const targetNormal = new Cesium.Cartesian3();
    ellipsoid.geocentricSurfaceNormal(target, targetNormal);
    const targetToCamera = new Cesium.Cartesian3();
    Cesium.Cartesian3.subtract(pos, target, targetToCamera);
    Cesium.Cartesian3.normalize(targetToCamera, targetToCamera);
    const up = viewer.camera.up;
    const right = viewer.camera.right;
    const normal = new Cesium.Cartesian3(-target.y, target.x, 0);
    const heading = Cesium.Cartesian3.angleBetween(right, normal);
    const cross = Cesium.Cartesian3.cross(target, up, new Cesium.Cartesian3());
    const orientation = cross.z;
    view.setRotation(orientation < 0 ? heading : -heading);
  } else {
    view.setRotation(viewer.camera.heading);
  }
}
function pickCenterPoint(scene) {
  const canvas = scene.canvas;
  const center = new Cesium.Cartesian2(canvas.clientWidth / 2, canvas.clientHeight / 2);
  return pickOnTerrainOrEllipsoid(scene, center);
}
function pickOnTerrainOrEllipsoid(scene, pixel) {
  const ray = scene.camera.getPickRay(pixel);
  const target = scene.globe.pick(ray, scene);
  return target || scene.camera.pickEllipsoid(pixel);
}
function calcResolutionForDistance(distance, latitude, scene, projection) {
  const canvas = scene.canvas;
  const camera = scene.camera;
  const fovy = camera.frustum.fovy;
  const metersPerUnit = projection.getMetersPerUnit();
  const visibleMeters = 2 * distance * Math.tan(fovy / 2);
  const relativeCircumference = Math.cos(Math.abs(latitude));
  const visibleMapUnits = visibleMeters / metersPerUnit / relativeCircumference;
  const resolution = visibleMapUnits / canvas.clientHeight;
  return resolution;
}
```

## 参考

[ol-cesium视角同步核心代码](https://github.com/openlayers/ol-cesium/blob/568a2b0de9cb01c1010cc5ca2bf9e4c3f720073f/src/olcs/Camera.js)

## 示例

[ol-cesium视角同步示例代码](https://kaifeiji.cc/demos/ol-cesium.html)
