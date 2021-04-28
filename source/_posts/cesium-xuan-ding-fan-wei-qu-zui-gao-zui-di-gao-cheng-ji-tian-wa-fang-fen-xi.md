---
title: 'Cesium选定范围取最高、最低高程及填挖方分析'
date: 2021-01-13 11:17:04
categories:
- 前端
tags:
- 三维
- Cesium
- 填挖方
- 算法
---

最近做一个填挖方分析的三维功能模块，受前人启发实现了一种取最高最低高程，以及填挖方分析的方法。

<!-- more -->

![Cesium填挖方分析](/post-images/cesium-xuan-ding-fan-wei-qu-zui-gao-zui-di-gao-cheng-ji-tian-wa-fang-fen-xi.png)

# 前人的启示

[Cesium- How to calculate highest point, lowest point, average altitude in a specified area around the click position?](https://stackoverflow.com/questions/40794293/cesium-how-to-calculate-highest-point-lowest-point-average-altitude-in-a-spec)

翻译如下：

Cesium - 在选定区域如何计算最高点、最低点、平均高程？

Qandeel Abbasi： 我的解决方案是在一个方形区域，通过双线性插值，取得范围内采样点的经纬度，然后使用 Cesium 提供的 sample terrain 接口，来获取所有采样点的高程，然后取得最高、最低，并计算平均值……

# 最高、最低高程

Qandeel Abbasi 的思路，其实就是通过采样获取最高、最低高程，采样点越密集、越多，精准度就越高。

但他在原答案中贴出的代码，是谷歌地图的 API……

通过查阅，我采用了更方便、通用的方式——turf。

```javascript
// turf格式的polygon
const turfPolygon = turf.polygon([[
    [115.81892851491955, 40.4802518813306],
    [115.81630221287354, 40.482671616134674],
    [115.81951377727235,40.4833706498091],
    [115.81892851491955, 40.4802518813306],
]]);
// 获取四至
const turfExtent = turf.bbox(turfPolygon);
// 在turfPolygon中按网格取样点，网格间距3米
const turfSamplePoints=turf.pointGrid(turfExtent,0.003,{
    units:'kilometers',
    mask: turfPolygon,
});
// 将turf取样点转为Cesium的取样点
const cesiumSamplePoints = [];
for (let i = 0; i < turfSamplePoints.features.length; i++) {
    const coord = turfSamplePoints.features[i].geometry.coordinates;
    cesiumSamplePoints.push(Cesium.Cartographic.fromDegrees(coord[0],coord[1]));
}
// 获取取样点的高程，使用11级地形，级别越高越精确，但更耗时
Cesium.when(Cesium.sampleTerrain(viewer.terrainProvider,11,cesiumSamplePoints),(updatedPoints) => {
    // 在更新后的取样点中，获得最高海拔和最低海拔
    let highest = 0;
   let lowest = 8888;
    updatedPoints.forEach(point=>{
    if (point.height>highest) {
        highest = point.height.toFixed(3);
    }
    if (point.height<lowest) {
        lowest = point.height.toFixed(3);
    }
});
```

# 填挖方分析

填挖方分析，采用了微积分的思想。

获取最高最低高程的过程中，已经在 polygon 内进行了采样。

可以利用这些采样点，将 polygon 平均划分成 n 个小区域。

每个小区域的面积乘以高程差，即为该小区域的挖方或填方体积，累加即可得总 的填挖方体积

```javascript
// 设定平整高程
const level = 620;
// 计算面积和单个取样点面积
const area = turf.area(turfPolygon).toFixed(3);
const diffArea = area / cesiumSamplePoints.length;
// 根据每个取样点与平整高程的差，累加挖方量和填方量
let cut = 0;
let fill = 0;
cesiumSamplePoints.forEach((point) => {
	if (point.height > level) {
		cut += (point.height - level) * diffArea;
	} else if (point.height < level) {
		fill += (level - point.height) * diffArea;
	}
});
```

# 参考

示例：

[cut&fill](http://kaifeiji.cc/demos/cutandfill.html)

完整代码：

[https://github.com/kaifeiji/cesium-examples/blob/master/cutandfill.html](https://github.com/kaifeiji/cesium-examples/blob/master/cutandfill.html)
