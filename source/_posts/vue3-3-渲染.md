---
title: vue3渲染优化
date: 2022-09-2 19:40:20
tags:
    - vue3
    - 源码
categories: [学习,笔记]
---

## render升级

在vue2中，知道了是将template模板转换为ast树。然后通过with function 生成render函数，最后转换为真实dom。
<!-- more -->
在vue3中，同样是这样的流程，但是做了许多优化。

## 静态标记

vue2与vue3 同样对静态节点进行了表记，保证了diff算法中不会去替换更新静态节点。


