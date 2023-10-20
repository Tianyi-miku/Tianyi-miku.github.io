---
title: echarts常用配置
date: 2021-07-18 14:50:28
tags:
    - echarts
categories:  [业务,插件]
   
---

## echatrs统计

# title

标题一般用的比较少，主要设置颜色大小距离。
<!-- more -->

```
title:{
  show: true, //是否显示
  text: '标题',
  padding: [5,5,5,5], //边距
  left: 20, //20% auto 都可以 right bottom top 同样 
  backgroundColor: 'rgb(128, 128, 128)',
  borderColor: '#ccc',  // borderWidth 宽度  borderRadius 圆角
  ...... 
}

```

## legend

选择器，主要用的多的就是距离，点击事件和显示的名称之类的。

```
legend:{
  show: true, //是否显示
  left: 20,   //20% auto 都可以 right bottom top 同样 
  with: 'auto', // height 也一样，可以自定义
  padding: 10,  //边距
  itemGap: 10,  //多个图例之间间隔
  textStyle: {
    color: 'red' //文本样式，包括颜色大小距离阴影文字省略等等。
  },
  data: [
    name: '系列1', 
    icon: 'circle',    // 强制设置图形为圆。
    textStyle: {
        color: 'red'   // 设置单个文本为红色
    }
  ],
  selector: false, //图例是否显示，
  ......
}
```

## xAxis yAxis

yAxis 与 xAxis 配置很相似。

```
xAxis:{
  show: true,
  position: 'bottom'， //位置默认在底部
  nameRotate：'15',    //名称旋转度,常用于X轴坐标显示不完全
  nameGap: 15,         //坐标与轴线之间的距离
  min: 20,             //最小值，可以传number string function类型 max最大值同样、
  scale：false,        //是否是脱离 0 值比例。设置成 true 后坐标刻度不会强制包含零刻度。在双数值轴的散点图中比较有用。设置 min 和 max 之后该配置项无效。
  interval: 10,        //强行设置间隔是多少，minInterval，maxInterval 设置最大间隔与最小间隔
  ......
 }

```

## dataZoom

```
dataZoom:{
  filterMode: 'filter' //模式。有一个维度在数据窗口外，整个数据项就会被过滤掉。还有'weakFilter'，'empty'，'none'模式，具体用的时候查看。
  start: 30, //缩放比例
  end: 70,
  .....
}

```

## tooltip

提示框一般来说用的比较多，可以自定义配置鼠标与图形交互显示，重点在axisPointer配置。
```
tooltip:{
  show: true, //是否显示
  trigger: 'item', //图形触发类型，包括'axis'坐标轴触发，'none'什么都不触发。
  axisPointer:{   
    type: 'line', //直线指示器，还包括'shadow' 阴影指示器，'none' 无指示器，'cross' 十字准星指示器。
    axis: 'auto', //指示器的坐标轴。 还包括'x', 'y', 'radius', 'angle'。
    label: {}, //包括label所有样式属性。
    animation: true, //动画
    position: [10, 10], //默认跟随鼠标，也可以指定位置
    formatter: '{b0}: {c0}<br />{b1}: {c1}',  //显示格式，支持模板字符串与回调函数(params: Object|Array, ticket: string, callback: (ticket: string, html: string)) => string | HTMLElement | HTMLElement[]
    ......
  }
}
```

## axisPointer

坐标轴显示器，一般用的比较少，用的时候查看官网、

```
axisPointer:{
   label: {}, //包括label所有样式属性。
   formatter: '', 与tooltip中formatter类似。
   .....
}

```

## series
图形数据.
```
series:[
  {
    type: 'line', //包括line,bar,pie...所有图形.
    label: {}, //包括label所有样式属性。
    tooltip: {}, //与tooltip类似.
    data: [
        // 维度X   维度Y   其他维度 ...
        [  3.4,    4.5,   15,   43],
        [  4.2,    2.3,   20,   91],
        [  10.8,   9.5,   30,   18],
        [  7.2,    8.8,   18,   57]
    ], //默认维度,根据type的图形类别,有不同数据格式.
  }
  ......
]

```

一般在开发中遇到的事件

## event

```
// 重置大小
window.addEventListener('resize', () => {
});
 //数据显示开关点击事件
 lineChart.off('legendselectchanged')
 lineChart.on("legendselectchanged", (params) => {
 })
 //监听图像双击,也可以单机click以及mouseup,mousedown鼠标各种操作,getZr可获取坐标值.
 lineChart.getZr().off('dblclick')
 lineChart.getZr().on("dblclick", (params) => {
 })
 //默认放大缩小事件
  lineChart.on("dataZoom", (params, event) => {})
//可以重写在echarts里面各种鼠标操作事件,以达到想要的交互
```

官网Api [echarts api](https://echarts.apache.org/zh/option.html#title)
