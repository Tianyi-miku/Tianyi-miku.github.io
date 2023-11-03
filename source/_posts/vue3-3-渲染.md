---
title: vue3优化
date: 2023-09-2 19:40:20
tags:
    - vue3
    - 源码
categories: [学习,笔记]
---

## 多根节点

Vue3 支持多个根节点，也就是 fragment
<!-- more -->
```
<template>
  <header></header>
  <main></main>
  <footer></footer>
</template>
```

## Composition API

这个就不多说,组合式Api

## Suspense 

渲染前展示的内容,于react中的差不多

# Teleport 

用的不多,只是一个DOM移动到外层


## 静态标记

vue3对静态节点进行了表记，保证了diff算法中不会去替换更新静态节点。

## cacheHandler 

事件缓存,不用每次去新建函数


## 打包

Tree-shaking模块化打包,vue3源码中大多数文件夹都是一个模块 
