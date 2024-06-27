---
title: mapbox基础配置
date: 2024-06-16 11:43:05
tags:
    - mapbox
categories: [业务,mapbox]
---

# mapbox
mapbox基本配置,包含mark点,线,热力图,自定义图标,切换图标,带动画的点等
![Alt text](/img/mapbox3.png)
<!-- more -->

点
```
new mapboxgl.Marker().setLngLat(intersection.features[0].geometry.coordinates).addTo(mapboxMap);
```

热力图需要注意模式,可以通过点胡总和或者插值算法等配置
```
      mapboxMap.addSource('earthquakes', {
        'type': 'geojson',
        'data': heatMapgeojson
      });
  mapboxMap.addLayer(
        {
          'id': 'earthquakes-heat',
          'type': 'heatmap',
          'source': 'earthquakes',
          'paint': {
            // // Increase the heatmap weight based on frequency and property magnitude
            'heatmap-weight': 0.1,
          }
        },
      );

```
​
切换显示与否
```
  mapboxMap.setLayoutProperty('layer-with-pulsing-dot', 'visibility', 'visible');
  mapboxMap.setLayoutProperty('layer-with-pulsing-dot', 'visibility', 'none');

```

动画点在官网上有例子,用canvas实现的