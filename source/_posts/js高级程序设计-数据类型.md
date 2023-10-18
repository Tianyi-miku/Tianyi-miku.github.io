---
title: js高级程序设计-数据类型
date: 2020-04-24 18:13:24
tags:
    - js
categories: [学习,JS]
---

## 基本数据类型

基本数据类型包括 string number null undefined Symbol Boolean.
<!-- more -->

通常判断基本类型的时候用到typeof。typeof是一个操作符，一般来说使用typeof()都可以判断其类型。但是注意一点，在typeof(null)中，返回的是"object"。
是因为object在二进制表达的时候是0000开头，而正好null以二进制表达也是0000。

## 复杂类型

Object 对象。