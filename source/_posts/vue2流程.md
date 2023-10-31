---
title: vue2 流程
date: 2023-03-7 21:12:58
tags:
    - vue2
    - 源码
categories: [学习,笔记]
---

## init

首先是初始化一个inti函数，挂载在Vue上，然后调用。
<!-- more -->

1.先将event事件绑定，父子组件关系确认。

然后触发beforcreate方法。

2.injections，提供inject与provide上下文，类似于react中context。接着声明响应式数据

触发creat方法

3.触发$mount方法模板转译。

调用beformount方法

4.渲染

创建一个el，渲染到页面。这时候渲染wather收集依赖，遇到更新的时候重新渲染。

5.销毁

触发destroyed,移除dom，事件以及响应式数据。


## 总结流程

1.先初始化组件方法，父子组件关系

2.初始化响应式数据，计算属性方法以及上下文。

3.查看用户是否传入了el，如果是模板将会编译，最终都是拿到render

4.渲染的时候watcher调用render触发依赖收集，所有的响应式数据dep属性都会挂载，以便后面更新时候找到对应的watcher。

5.更新时候diff算法。

6.销毁时候移除所有属性以及方法。