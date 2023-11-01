---
title: canvas瀑布图
date: 2023-08-22 15:22:35
tags:
    - canvas
categories: [业务]
---
![Alt text](/img/canvas1.gif)
<!-- more -->
界面
```
 <div className='waterfall'>
      <canvas className='legend' ref={Ilegend}></canvas>
      <canvas className='canvas' ref={waterFall}></canvas>
  </div>
```
全局变量
```
let getCtx: any = null;
let waterFallWidth = 0
let waterFallHeight = 0
let waterFallCopyList: any = []
let waterFallIndex = 0
```

颜色用colormap插件生成
```
  let Icolormap = colormap({
    colormap: 'portland',
    nshades: 150,
    format: 'rba',
    alpha: 1,
  })
```

左边柱状图
```
const Ilegend = useRef(null);

function createLegendCanvas() {
    const legendRefs: any = Ilegend.current;
    const legend = legendRefs.getContext('2d')
    let legendCanvas = document.createElement('canvas')
    legendCanvas.width = 1
    let legendCanvasTemporary: any = legendCanvas.getContext('2d')
    const imageData = legendCanvasTemporary.createImageData(1, Icolormap.length)
    for (let i = 0; i < Icolormap.length; i++) {
      const color = Icolormap[i]
      imageData.data[imageData.data.length - i * 4 + 0] = color[0]
      imageData.data[imageData.data.length - i * 4 + 1] = color[1]
      imageData.data[imageData.data.length - i * 4 + 2] = color[2]
      imageData.data[imageData.data.length - i * 4 + 3] = 255
    }
    legendCanvasTemporary.putImageData(imageData, 0, 0)
    legend.drawImage(legendCanvasTemporary.canvas,
      0, 0, 1, Icolormap.length, 50, 0, 200, legend.canvas.height)
  }
```

频谱图做法是为颜色对应数值，数组进栈头部出栈，canvas清除
```
  function queryChartList() {
    if (prop.WaterfallData && prop.WaterfallData.length) {
      let data: any[] = prop.WaterfallData.map((item: number) => { return item / 10 })
      //@ts-expect-error
      waterFallWidth = waterFall.current?.width
      waterFallHeight = 60 //默认
      if (waterFall === null) {
        createWaterFallCanvas()
      }
      rowToImageData(data)
      waterFallCopyList.unshift(data)
      waterFallIndex++
      if (waterFallIndex > waterFallHeight) {
        waterFallCopyList.pop()
      }
    }
  }

  function createWaterFallCanvas() {
    const canvasDimensions: any = waterFall.current;
    getCtx = canvasDimensions?.getContext("2d");
    getCtx.canvas.width = getCtx.canvas?.offsetWidth
  }


    function rowToImageData(data: any[]) {
    if (getCtx) {
      const canvasWidth = getCtx.canvas.offsetWidth;
      const scale = data.length / canvasWidth;
      let canvasHeight = Math.floor(getCtx.canvas.offsetHeight / waterFallHeight);
      let imgOld = getCtx.getImageData(0, 0, waterFallWidth, canvasHeight * waterFallIndex + 1);

      const imageData = getCtx.createImageData(canvasWidth, 1);
      for (let i = 0; i < imageData.data.length; i += 4) {
        const index = Math.floor(i / 4 * scale);
        const cindex = colorMapData(data[index], 0, 80);
        const color = Icolormap[cindex];
        imageData.data[i + 0] = color[0];
        imageData.data[i + 1] = color[1];
        imageData.data[i + 2] = color[2];
        imageData.data[i + 3] = 255;
      }
      for (let i = 0; i < canvasHeight; i++) {
        getCtx.putImageData(imageData, 0, i);
      }
      if (isClean) {
        getCtx.clearRect(0, 0, getCtx.canvas.width, getCtx.canvas.height);
        setIsClean(false);
      } else {
        getCtx.putImageData(imgOld, 0, canvasHeight);
      }
    }
  }
```

全部代码
```
import React, { useEffect, useMemo, useRef, useState } from 'react'
import colormap from 'colormap'
import "./waterfall.less"

let getCtx: any = null;
let waterFallWidth = 0
let waterFallHeight = 0
let waterFallCopyList: any = []
let waterFallIndex = 0

const Waterfall = (prop: any) => {
  let waterFall = useRef(null);
  const Ilegend = useRef(null);
  let maxNum = 10
  let minNum = 0
  const [isClean, setIsClean] = useState(false)


  let Icolormap = colormap({
    colormap: 'portland',
    nshades: 150,
    format: 'rba',
    alpha: 1,
  })

  useEffect(() => {
    createLegendCanvas()
    createWaterFallCanvas()
    return () => {
    }
    // eslint-disable-next-line
  }, [])

  useEffect(() => {
    setIsClean(true)
    return () => {
    }
  }, [prop.zoom])


  useEffect(() => {
    if (prop.WaterfallData) {
      queryChartList()
    }
    return () => {
    }
    // eslint-disable-next-line
  }, [prop.WaterfallData])

  useEffect(() => {
    if (prop.clean) {
      getCtx.clearRect(0, 0, getCtx.canvas.width, getCtx.canvas.height)
    }
    return () => {
    }
  }, [prop.clean])


  function createLegendCanvas() {
    const legendRefs: any = Ilegend.current;
    const legend = legendRefs.getContext('2d')
    let legendCanvas = document.createElement('canvas')
    legendCanvas.width = 1
    let legendCanvasTemporary: any = legendCanvas.getContext('2d')
    const imageData = legendCanvasTemporary.createImageData(1, Icolormap.length)
    for (let i = 0; i < Icolormap.length; i++) {
      const color = Icolormap[i]
      imageData.data[imageData.data.length - i * 4 + 0] = color[0]
      imageData.data[imageData.data.length - i * 4 + 1] = color[1]
      imageData.data[imageData.data.length - i * 4 + 2] = color[2]
      imageData.data[imageData.data.length - i * 4 + 3] = 255
    }
    legendCanvasTemporary.putImageData(imageData, 0, 0)
    legend.drawImage(legendCanvasTemporary.canvas,
      0, 0, 1, Icolormap.length, 50, 0, 200, legend.canvas.height)
  }


  function createWaterFallCanvas() {
    const canvasDimensions: any = waterFall.current;
    getCtx = canvasDimensions?.getContext("2d");
    getCtx.canvas.width = getCtx.canvas?.offsetWidth
  }


  //单行图像
  function rowToImageData(data: any[]) {
    if (getCtx) {
      const canvasWidth = getCtx.canvas.offsetWidth;
      const scale = data.length / canvasWidth;
      let canvasHeight = Math.floor(getCtx.canvas.offsetHeight / waterFallHeight);
      let imgOld = getCtx.getImageData(0, 0, waterFallWidth, canvasHeight * waterFallIndex + 1);

      const imageData = getCtx.createImageData(canvasWidth, 1);
      for (let i = 0; i < imageData.data.length; i += 4) {
        const index = Math.floor(i / 4 * scale);
        const cindex = colorMapData(data[index], 0, 80);
        const color = Icolormap[cindex];
        imageData.data[i + 0] = color[0];
        imageData.data[i + 1] = color[1];
        imageData.data[i + 2] = color[2];
        imageData.data[i + 3] = 255;
      }
      for (let i = 0; i < canvasHeight; i++) {
        getCtx.putImageData(imageData, 0, i);
      }
      if (isClean) {
        getCtx.clearRect(0, 0, getCtx.canvas.width, getCtx.canvas.height);
        setIsClean(false);
      } else {
        getCtx.putImageData(imgOld, 0, canvasHeight);
      }
    }
  }


  //返回对于颜色库
  function colorMapData(data: number, outMin: number, outMax: number) {
    if (data <= minNum) {
      return outMin
    } else if (data >= maxNum) {
      return outMax
    }
    return Math.round(((data - minNum) / (maxNum - minNum)) * outMax)
  }

  function queryChartList() {
    if (prop.WaterfallData && prop.WaterfallData.length) {
      let data: any[] = prop.WaterfallData.map((item: number) => { return item / 10 })
      //@ts-expect-error
      waterFallWidth = waterFall.current?.width
      waterFallHeight = 60 //默认
      if (waterFall === null) {
        createWaterFallCanvas()
      }
      rowToImageData(data)
      waterFallCopyList.unshift(data)
      waterFallIndex++
      if (waterFallIndex > waterFallHeight) {
        waterFallCopyList.pop()
      }
    }
  }

  return (
    <div className='waterfall'>
      <canvas className='legend' ref={Ilegend}></canvas>
      <canvas className='canvas' ref={waterFall}></canvas>
    </div>
  )
}

export default Waterfall
```