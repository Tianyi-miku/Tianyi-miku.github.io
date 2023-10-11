---
title: vue2方法
date: 2022-07-4 19:45:53
tags:
    - vue2
    - 源码
categories: [学习,笔记]
---

## Vue.use

vue.use就是引入插件，所有的插件都是函数，将Vue作为参数，传入到插件函数中，实现方法后，在全局Vue上就能看到。如同vue router vuex。
<!-- more -->

## Vue.extend

所有组件都是Vue.extend创建的。

可以通过传入选项，手动挂载组件！

## Vue.filter

过滤器。根据名称去找到对应的函数，对数据加工后，返回处理的数据。不会改变原数据。

因为编译浪费，vue3已经抛弃此方法。

## v-once

标记静态节点，下次直接跳过渲染。vue3是memo!

## Vue.mixin

混入。局部混入或全局，mergeOption方法，用于复用逻辑。在vue3中，不需要这个了,用的compositionAPi。


## solt

插槽，普通插槽 具名插槽 作用域插槽。

普通插槽父组件渲染直接替换，作用域插槽子组件渲染，内部函数替换。

## .async

v-model默认绑定一个，.async可以多个。 vue3移除。