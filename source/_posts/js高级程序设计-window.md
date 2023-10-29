---
title: js高级程序设计-window对象
date: 2020-05-06 18:51:20
tags:
    - js
categories: [学习,JS]
---
## window

BOM核心window对象,浏览器的实例
<!-- more -->
```
var age = 29; 
var sayAge = () => alert(this.age); 
alert(window.age); // 29
```
视窗高度outerWidth,outerHeight 
innerHeight,innerWidth 不包含浏览器边框和工具栏 document.body.clientWidth document.body.clientHeight 可视窗口大小

resizeTo resizeBy 缩放

## 滚动

scroll scrollTo scrollBy
```
window.scrollBy(0, 100); //于当前视口向下滚动 100 像素,相对于当前渲染窗口
window.scrollTo(0, 0); //滚动到左上角
window.scrollTo(100, 100); //滚动左上角100像素 ,相对于原点

```

## 常用方法

window.open 打开新窗口 replace 替换导航到新的URL

setTimeout() 定时器 clearTimeout() 取消定时器

setInterval() 事件循环定时器

alert() confirm() prompt() 系统提示弹框

location.hash location.host location.hostname location.href location.username  location.password location.search

navigator返回浏览器属性, NavigatorID  NavigatorLanguage NavigatorOnLine NavigatorContentUtils NavigatorStorage 和 NavigatorUserMedia等

 window.navigator.plugins //插件 navigator.geolocation //地理位置

 screen.height  screen.width  screen.top  screen.left

 history.go() //导航,历史记录  

 pushState() //触发 window 对象上的 popstate 事件