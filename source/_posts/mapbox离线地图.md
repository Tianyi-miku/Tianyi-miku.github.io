---
title: mapbox离线
date: 2024-05-17 15:23:34
tags:
    - mapbox
categories: [业务,mapbox]
---

# mapbox
mapbox离线地图
![Alt text](/img/mapbox1.png)
<!-- more -->

离线使用mapbox 先初始化地图，主要配置style
```
  useEffect(() => {
    if (mapboxMap.current) return
    const isOnline = config.isOnline
    // 初始化threebox
    mapboxMap.current = new mapboxgl.Map({
      container: "mapbox-map", // container id
      style: style, // style URL
      //@ts-ignore
      projection: 'globe',
      center: center, // starting position [lng, lat]
      zoom: zoom, // starting zoom
      accessToken: 'xxx',
    })
    mapboxMap.current.on('style.load', function () {
      setloading(true)
    })
    /*删除mapbox自带logo*/
    mapboxMap.current._logoControl && mapboxMap.current.removeControl(mapboxMap.current._logoControl);
    return () => {
      setloading(false)
      mapboxMap.current = null
    }
    //eslint-disable-next-line
  }, [])

```

需要准备离线资源：包括字体sprite，精灵图glyphs,可以在官网上下载也可以找资源。
这里我们将Proxymapbox作为代理，转到nginx资源目录下。roadmap则为地图文件，这里暂时使用png
```
const style: any = {
  version: 8,
  sprite: 'http://localhost:3000/Proxymapbox/style/sprite',
  glyphs: "http://localhost:3000/Proxymapbox/glyphs/mapbox/{fontstack}/{range}.pbf",
  "sources": {
    "osm-tiles": {
      "type": "raster",
      'tiles': [
        "/Proxymapbox/roadmap/{z}/{x}/{y}.png"
      ],
      'tileSize': 256
    }
  },
  "layers": [{
    "id": "simple-tiles",
    "type": "raster",
    "source": "osm-tiles",
    "minzoom": 0,
    "maxzoom": 22
  }]
}
```
​
代理
```
  "/Proxymapbox": {
        target: `http://127.0.0.1:9100`, //资源所在地址
        changeOrigin: true,
        pathRewrite: { '^/Proxymapbox': '' },
        changeOrign: true,
        logLevel: 'debug'
      },
```