---
title: mapbox自定义标注卡顿问题
date: 2024-05-31 9:48:30
tags:
    - mapbox
categories: [业务,mapbox]
---

# mapbox
mapbox自定义标注，一般是通过Mark来添加，但是数量过多后会明显卡顿，这里通过自定义标注，和新增图层来将所有标注合成一个图层
![Alt text](/img/mapbox2.png)
<!-- more -->

首先自定义标注的时候，用自己的图标。mapbox不支持svg，可以手动转成img对象
```
//element.type.value为类型
   function addImage(url: any, element: any) {
    return new Promise((resolve, reject) => {
      mapboxMap.loadImage(url, function (error: any, image: any) {
        if (error) reject(error);;
        mapboxMap.addImage(element.type.value, image);
        resolve(true);
      });
    });
  }

```

将所有需要标注的数据，合成一个Layer
```
 let featureslist = []
   for (let index = 0; index < station.length; index++) {
    featureslist.push(
            {
              "type": "Feature",
              "geometry": {
                "type": "Point",
                // 图片和文字在地图中的坐标
                "coordinates": [element.position.longitude, element.position.latitude]
              },
              "properties": {
                element: element,
                icon: element.type.value,
              },
            }
       )
   }

    const featuresObj = {
      "type": "FeatureCollection",
      "features": features,
    }
      mapboxMap.addSource('pointSouce', {
        "type": "geojson",
        "data": featuresObj,
      })
     // 更新pointSouce数据为
      mapboxMap.getSource('pointSouce').setData(featuresObj)

    mapboxMap.addLayer({
      id: 'points',
      type: "symbol",
      source: 'pointSouce',
      layout: {
        // 加载图片的id
        "icon-image": ['get', 'icon'],
        // 图片大小
        "icon-size": 0.8
      },
    });

```
​
给点标注添加点击事件，显示不同详情
```
   mapboxMap.on('mouseenter', 'points', function () {
      mapboxMap.getCanvas().style.cursor = 'pointer';
    });
    // // 鼠标在离开标注时恢复鼠标样式
    mapboxMap.on('mouseleave', 'points', function () {
      mapboxMap.getCanvas().style.cursor = '';
    });
    // 标注要素添加点击事件
    mapboxMap.on('click', 'points', async function (e: any) {
      // 标注的坐标
      let coordinates = e.features[0].geometry.coordinates.slice();
      const element = JSON.parse(e.features[0].properties.element)
      const info: any = await getInfo(element.type.value, element.code)
      // 弹出框展示的内容
      let description = info || `<div>${element.name}</div>`;
      // 如果有重复的地图，保证在重复的标注上正常显示
      while (Math.abs(e.lngLat.lng - coordinates[0]) > 180) {
        coordinates[0] += e.lngLat.lng > coordinates[0] ? 360 : -360;
      }
      // 标注绑定弹出框
      mapPopup = new mapboxgl.Popup({ offset: [0, -40], className: 'mapboxglpopup' }).setLngLat(coordinates).setHTML(description).addTo(mapboxMap);
    });
```

当前地图收缩放大会聚合图标，如果有不同类型聚合的，可以分类型添加不同layer，通过过滤layer api来轻松显示不同类型标注。