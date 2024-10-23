---
layout: post
title: 地图坐标分类和转换关系
categories:
  - Program
description: Refactoring
keywords: Program
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
## 分类

### WGS84

wgs84(world geodetic system 1984)

经度（longitude），15度经度一小时，东经120度，东八区，东西为经，经线半圆长线，long, longitude

格林威治天文台，London， 本初子午线， 0度经线，东0到180度， 西0到-180度

维度（latitude)，南北为维，纬线为圆形，周而复来，latitude，赤道为0度，北半球0到90度，南半球0到-90度

 应用范围：GPS定位，国际地图和导航系统

### GCJ-02
中国国家测绘局制定的地理坐标系统，基于WGS84进行了加密偏移

应用范围：中国常用地图，高德地图，腾讯地图
注：百度地图使用BD-09坐标，在GCJ-02基础上进行再次加密

### Cartesian 
使用直角坐标表示点的空间位置，通过X，Y（或者Z）表示平面或者空间中的点，

地理数据的局部投影，方便特定区域的精确测量
应用范围：小范围空间分析和工程测绘
例如一个城市的工程地图可以使用Cartesian坐标进行局部放大分析









