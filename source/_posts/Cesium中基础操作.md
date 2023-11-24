---
title: Cesium中基础操作
date: 2023-11-24 16:25:30
tags:
    - Cesium
categories: [业务]
---
![Alt text](/img/cesium1.gif)
<!-- more -->
新增点
```
import * as Cesium from "cesium";
  //容器id
  const viewer = new Cesium.Viewer('cesiumContainer', {
    geocoder: false,
    homeButton: false,
    sceneModePicker: false,
    baseLayerPicker: false,
    navigationHelpButton: false,
    animation: false,
    timeline: false,
    fullscreenButton: false,
    vrButton: false,
  })
  const elementui = viewer.cesiumWidget.creditContainer as HTMLElement
  elementui.style.display = "none";


  const point2 = {
    position: Cesium.Cartesian3.fromDegrees(-75.59777, 40.03883),
    point: {
      show: true, // default
      color: Cesium.Color.SKYBLUE, // default: WHITE
      pixelSize: 10, // default: 1
      outlineColor: Cesium.Color.YELLOW, // default: BLACK
      outlineWidth: 3, // default: 0
    },
  }

  const point3 = {
    position: Cesium.Cartesian3.fromDegrees(-75.29777, 39.53883),
    point: {
      show: true, // default
      color: Cesium.Color.SKYBLUE, // default: WHITE
      pixelSize: 20, // default: 1
      outlineColor: Cesium.Color.YELLOW, // default: BLACK
      outlineWidth: 3, // default: 0
    },
  }
  // 点
  viewer.entities.add({
    position: Cesium.Cartesian3.fromDegrees(-75.166493, 39.9060534),
    point: {
      pixelSize: 5,
      color: Cesium.Color.RED,
      outlineColor: Cesium.Color.WHITE,
      outlineWidth: 2
    },
    label: {
      text: '公园point',
      font: '14pt monospace',
      style: Cesium.LabelStyle.FILL_AND_OUTLINE,
      outlineWidth: 2,
      verticalOrigin: Cesium.VerticalOrigin.TOP,
      pixelOffset: new Cesium.Cartesian2(0, 32)
    }
  })

  viewer.entities.add(point2)
  viewer.entities.add(point3)

  viewer.zoomTo(viewer.entities)
```
新增线
```
  viewer.entities.add({
    name: "polyline",
    polyline: {
      positions: Cesium.Cartesian3.fromDegreesArrayHeights([
        -75,
        45,
        500000,
        -125,
        45,
        500000,
      ]),
      width: 4,
      material: new Cesium.PolylineDashMaterialProperty({
        color: Cesium.Color.CYAN,
      }),
    },
  });
```
新增面
```
 viewer.entities.add({
    position: Cesium.Cartesian3.fromDegrees(-95.0, 40.0, 100000.0),
    name: "绿色旋转外边框椭圆ellipse",
    ellipse: {
      semiMinorAxis: 150000.0, //椭圆的半长轴的长度（以米为单位）。
      semiMajorAxis: 300000.0,
      // extrudedHeight: 200000.0,
      rotation: Cesium.Math.toRadians(45),
      material: Cesium.Color.BLUE.withAlpha(0.5),
      outline: true,
    },
  });
```
点击事件新增广告牌
```
  handler.setInputAction((movement: any) => {
    var position = viewer.scene.camera.pickEllipsoid(movement.position, viewer.scene.globe.ellipsoid);
    if (state.value1 === "piont") {
      addPoint(position);
    }
    if (state.value1 === "billboard") {
      addBillboard(position);
    }
  }, Cesium.ScreenSpaceEventType.LEFT_CLICK);

  const board = {
    position: position,
    billboard: {
      image: "./img/gy/tx.jpg",
      show: true, // default
      width: 25, // default: undefined
      height: 25, // default: undefined
      heightReference: Cesium.HeightReference.CLAMP_TO_GROUND, // 相对高度
    },
  };
```

展示模型
![Alt text](/img/cesium1.png)
```
  const heading = Cesium.Math.toRadians(135)
  const pitch = 0
  const roll = 0
  const hpr = new Cesium.HeadingPitchRoll(heading, pitch, roll)

  const entity = viewer.entities.add({
    name: 'CesiumDrone', // 模型的名称
    position: Cesium.Cartesian3.fromDegrees(-75.59777, 40.03883),
    orientation: Cesium.Transforms.headingPitchRollQuaternion(
      Cesium.Cartesian3.fromDegrees(-75.59777, 40.03883),
      hpr
    ), // 模型的观察视角
    model: {
      uri: './model/abby.glb', //路径为绝对路径
      minimumPixelSize: 128,
      maximumScale: 20000,
    },
  })

  viewer.trackedEntity = entity
```
加载json 3DTiles
```
  const tileset = viewer.scene.primitives.add(
    new Cesium.Cesium3DTileset({
      url: './model/Tileset/tileset.json',
    })
  );

  tileset.readyPromise
    .then(function (tileset: any) {
      viewer.scene.primitives.add(tileset);
      viewer.zoomTo(
        tileset,
        new Cesium.HeadingPitchRange(
          0.0,
          -0.5,
          tileset.boundingSphere.radius * 2.0
        )
      );
    })
    .catch(function (error: any) {
      console.log(error);
    });
```
添加波动与扫描 带shader渲染

坐标
```
  const point: any = {
    id: new Date().getTime(),
    position: position,
    color: Cesium.Color.MEDIUMTURQUOISE.withAlpha(0.5),
    duration: 5000,
    border: 10,
    radius: 2000,
    circleMode: 'CircleScan'
  };
  addscan(point, viewer)

```
坐标转换
```
export function transformCartesianToWGS84(cartesian: any) {
  if (cartesian) {
    var ellipsoid = Cesium.Ellipsoid.WGS84
    var cartographic = ellipsoid.cartesianToCartographic(cartesian)
    return {
      lng: Cesium.Math.toDegrees(cartographic.longitude),
      lat: Cesium.Math.toDegrees(cartographic.latitude),
      alt: cartographic.height
    }
  }
}

//坐标转换 84转笛卡尔
export function transformWGS84ToCartesian(position: any, alt?: any,) {
  return position
    ? Cesium.Cartesian3.fromDegrees(
      position.lng || position.lon,
      position.lat,
      position.alt = alt || position.alt,
      Cesium.Ellipsoid.WGS84
    )
    : Cesium.Cartesian3.ZERO
}
```
波动 与 扫描
```
import { transformCartesianToWGS84, transformWGS84ToCartesian } from "../trasform";
import _getCircleScanShader from "../shader/circleShader";
import * as Cesium from "cesium";
export function addscan(Iposition: any, _viewer: any,) {

  const id = Iposition.id
  const cartesian = Iposition.position

  const cartesian3Center = cartesian;
  const cartesian4Center = new Cesium.Cartesian4(
    cartesian3Center.x,
    cartesian3Center.y,
    cartesian3Center.z,
    1
  )
  const position: any = transformCartesianToWGS84(cartesian)
  const cartesian3Center1 = transformWGS84ToCartesian(
    {
      lng: position.lng,
      lat: position.lat,
      alt: position.alt + 500
    }
  )
  const cartesian4Center1 = new Cesium.Cartesian4(
    cartesian3Center1.x,
    cartesian3Center1.y,
    cartesian3Center1.z,
    1
  )

  const _time = new Date().getTime()
  const _delegate = new Cesium.PostProcessStage({
    name: id,
    fragmentShader: _getCircleScanShader({ get: true, border: Iposition.border }) ?? '',
    uniforms: {
      u_scanCenterEC: () => {
        return Cesium.Matrix4.multiplyByVector(
          _viewer.camera._viewMatrix,
          cartesian4Center,
          new Cesium.Cartesian4()
        )
      },
      u_scanPlaneNormalEC: () => {
        const temp = Cesium.Matrix4.multiplyByVector(
          _viewer.camera._viewMatrix,
          cartesian4Center,
          new Cesium.Cartesian4()
        )
        const temp1 = Cesium.Matrix4.multiplyByVector(
          _viewer.camera._viewMatrix,
          cartesian4Center1,
          new Cesium.Cartesian4()
        )
        const _scratchCartesian3Normal = new Cesium.Cartesian3()
        _scratchCartesian3Normal.x = temp1.x - temp.x
        _scratchCartesian3Normal.y = temp1.y - temp.y
        _scratchCartesian3Normal.z = temp1.z - temp.z
        Cesium.Cartesian3.normalize(
          _scratchCartesian3Normal,
          _scratchCartesian3Normal
        )
        return _scratchCartesian3Normal
      },
      u_radius: () => {
        if (Iposition.circleMode == 'CircleScan') {
          return (
            (Iposition.radius * ((new Date().getTime() - _time) % Iposition.duration)) /
            Iposition.duration
          )
        } else {
          return Iposition.radius
        }
      },
      u_scanColor: Iposition.color,
    }
  })

  _viewer.scene.postProcessStages.add(_delegate)

  return _delegate;
}
```

```
import * as Cesium from "cesium";
import { transformCartesianToWGS84, transformWGS84ToCartesian } from "../trasform";
import _getRadarScanShader from "../shader/scanCircleShader";
export function setRadarScanEffect(options: any, _viewer: any) {
  if (options && options.position) {

    const id = options.id
    const cartesian = options.position
    const duration = options.duration
    const border = options.border
    const width = options.width ?? 10
    const radius = options.radius
    const color = options.color

    const cartesian3Center = cartesian
    const cartesian4Center = new Cesium.Cartesian4(
      cartesian3Center.x,
      cartesian3Center.y,
      cartesian3Center.z,
      1
    )
    const position: any = transformCartesianToWGS84(cartesian)
    const cartesian3Center1 = transformWGS84ToCartesian(
      {
        lng: position.lng,
        lat: position.lat,
        alt: position.alt + 500
      }
    )
    const cartesian4Center1 = new Cesium.Cartesian4(
      cartesian3Center1.x,
      cartesian3Center1.y,
      cartesian3Center1.z,
      1
    )

    const cartesian3Center2 = transformWGS84ToCartesian(
      {
        lng: position.lng + 0.001,
        lat: position.lat,
        alt: position.alt
      }
    )
    const cartesian4Center2 = new Cesium.Cartesian4(
      cartesian3Center2.x,
      cartesian3Center2.y,
      cartesian3Center2.z,
      1
    )
    const _time = new Date().getTime()
    const _RotateQ = new Cesium.Quaternion()
    const _RotateM = new Cesium.Matrix3()
    const _scratchCartesian4Center = new Cesium.Cartesian4()
    const _scratchCartesian4Center1 = new Cesium.Cartesian4()
    const _scratchCartesian4Center2 = new Cesium.Cartesian4()
    const _scratchCartesian3Normal = new Cesium.Cartesian3()
    const _scratchCartesian3Normal1 = new Cesium.Cartesian3()
    const _delegate = new Cesium.PostProcessStage({
      name: id,
      fragmentShader: _getRadarScanShader({ border: border, width: width, get: true }) ?? '',
      uniforms: {
        u_scanCenterEC: function () {
          return Cesium.Matrix4.multiplyByVector(
            _viewer.camera._viewMatrix,
            cartesian4Center,
            _scratchCartesian4Center
          )
        },
        u_scanPlaneNormalEC: function () {
          const temp = Cesium.Matrix4.multiplyByVector(
            _viewer.camera._viewMatrix,
            cartesian4Center,
            _scratchCartesian4Center
          )
          const temp1 = Cesium.Matrix4.multiplyByVector(
            _viewer.camera._viewMatrix,
            cartesian4Center1,
            _scratchCartesian4Center1
          )
          _scratchCartesian3Normal.x = temp1.x - temp.x
          _scratchCartesian3Normal.y = temp1.y - temp.y
          _scratchCartesian3Normal.z = temp1.z - temp.z
          Cesium.Cartesian3.normalize(
            _scratchCartesian3Normal,
            _scratchCartesian3Normal
          )
          return _scratchCartesian3Normal
        },

        u_scanLineNormalEC: function () {
          const temp = Cesium.Matrix4.multiplyByVector(
            _viewer.camera._viewMatrix,
            cartesian4Center,
            _scratchCartesian4Center
          )
          const temp1 = Cesium.Matrix4.multiplyByVector(
            _viewer.camera._viewMatrix,
            cartesian4Center1,
            _scratchCartesian4Center1
          )
          const temp2 = Cesium.Matrix4.multiplyByVector(
            _viewer.camera._viewMatrix,
            cartesian4Center2,
            _scratchCartesian4Center2
          )

          _scratchCartesian3Normal.x = temp1.x - temp.x
          _scratchCartesian3Normal.y = temp1.y - temp.y
          _scratchCartesian3Normal.z = temp1.z - temp.z

          Cesium.Cartesian3.normalize(
            _scratchCartesian3Normal,
            _scratchCartesian3Normal
          )

          _scratchCartesian3Normal1.x = temp2.x - temp.x
          _scratchCartesian3Normal1.y = temp2.y - temp.y
          _scratchCartesian3Normal1.z = temp2.z - temp.z

          const tempTime =
            ((new Date().getTime() - _time) % duration) / duration
          Cesium.Quaternion.fromAxisAngle(
            _scratchCartesian3Normal,
            tempTime * Cesium.Math.PI * 2,
            _RotateQ
          )
          Cesium.Matrix3.fromQuaternion(_RotateQ, _RotateM)
          Cesium.Matrix3.multiplyByVector(
            _RotateM,
            _scratchCartesian3Normal1,
            _scratchCartesian3Normal1
          )
          Cesium.Cartesian3.normalize(
            _scratchCartesian3Normal1,
            _scratchCartesian3Normal1
          )
          return _scratchCartesian3Normal1
        },
        u_radius: radius,
        u_scanColor: color
      }
    })

    _viewer.scene.postProcessStages.add(_delegate)

    return _delegate;
  }
}
```