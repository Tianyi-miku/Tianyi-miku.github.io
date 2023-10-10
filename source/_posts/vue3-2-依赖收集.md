---
title: vue3依赖收集
date: 2022-08-23 17:2:13
tags:
    - vue3
    - 源码
categories: [学习,笔记]
---

在vue2中，依赖收集主要是通过观察订阅者模式来完成的。主要是通过Dep类，render在触发get的时候收集属性对应的watcher，修改属性触发dep.notify方法。

<!-- more -->

在vue3中，思路其实和vue2是差不多的。只是实现方式上有所不同。

vue3 同样在render的时候，会触发属性的get方法，track函数记录,全局effect会收集所对应的依赖。

在更新之后，会调用trgger方法通知更新。


他们都是声明一个全局变量，vue2 是Depp，vue3是map结构属性与effect映射。

然后渲染的收，render属性取值，触发get方法的时候，将对应的属性记录在全局变量中。

在某个属性set更新之后，vue2是notify,vue3 是将会通知对应的wacher或者effect更新。

## watch 与watcheffect

watch 与watcheffect底层逻辑差不多。

都是添加一个effect，在依赖改变的时候，执行回调函数。

只是watch会侦听一个或多个响应式数据，在其改变的时候执行自定义回调函数。

warcheffect会立即执行一个函数，不需要手动去侦听当中的响应式数据，自动追踪里面的依赖，当依赖改变的时候，会重新执行这个函数。

有点类似于react中的副作用函数。

watch手动收集依赖,当其改变时候才执行，watcheffect是自动收集依赖并立刻执行。
