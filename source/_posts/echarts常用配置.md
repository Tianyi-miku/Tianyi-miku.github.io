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
}
```
