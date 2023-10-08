---
title: vue2计算属性
date: 2022-06-2 11:16:34
tags:
    - vue2
    - 源码
categories: 学习
---
## 依赖收集

之前说到每一个属性触发get 都有一个dep函数。

因为页面要对新增的属性增加收集，数组嵌套也要循环去收集。

所以在Observe中，每个对象都声明一个new dep;

在get中，给每个对象也都添加dep.depend()方法，这样就能实现依赖收集了。

嵌套的数组，因为前面只讲了重写了方法，并没有对修改的具体内容进行收集，也是通过这里新添加dep.depend()方法，去收集依赖的。

$Set原理也是基于此，这样，数据更新的时候，也会重新渲染页面！
<!-- more -->


## 计算属性

计算属性不会收集依赖，只会让自己的依赖属性去收集依赖。dirty为false与true控制是否触发依赖get. 流程如下：

渲染Watcher执行的时候，遇到computed，生成一个计算属性watcher，存入Watcher执行栈中。

计算属性Watcher先去取值触发get方法。然后对象属性当中dep存放的就是计算属性的Watcher，这时候修改了属性之后，更新的只是计算属性中的watcher。

计算属性计算完之后，缓存计算结果,，如果值不变，下次不会再渲染，直接取缓存结果。

这时候为了让dep中也有渲染的watcher，循环去找属性的dep依赖的watcher，添加到dep中来！这样才能使修改属性后，组件中属性也能同步渲染！


流程：第一次，渲染Wacher入栈，渲染到computed的值，添加computed watcher ，computed watcher 先找到自己依赖的参数，触发计算，缓存计算结果。然后Wather栈中还有渲染watcher，同时添加到参数中的dep，渲染wathcer也能找到属性中对应的dep,实现页面渲染！

页面修改computed 依赖的参数后，依赖的参数收集的watcher通知 computed 的值改变了，设置dirty为true，表示要重新计算。这时候依赖的参数watcher通知页面中的属性更新，重新计算拿到新的值，最后将dirty设置为false，如果下次拿computed的值，而依赖的参数没变，就会取缓存。


## watch

渲染watcher遇到Watch的值，创建一个watch Watcher 放入 watcher执行栈。

先将页面中的老值存着，然后触发get获取到新的值 将新老值返回给watch.