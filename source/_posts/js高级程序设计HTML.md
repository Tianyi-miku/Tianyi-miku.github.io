---
title: js高级程序设计-HTML中的js
date: 2020-04-22 15:23:49
tags:
    - js
categories: [学习,JS]
---

## html中的js

将js 插入html中，主要是通过`<srcipt></script>`标签实现的。
<!-- more -->

## 基本属性

async

字面意思，是异步的。直接下载内容，但是不影响页面其他的动作，对外部脚本有效。

defer

等待页面解析后，才下载资源。等待最后执行。

charset 

使用src属性指定代码集，大多数浏览器不在意，用的少。

crossorigin 

跨域。配置相关请求的CORS（跨源资源共享）设置。

integrity

对比加密签名，验证完整性，在CDN或者分发网络中，可用于确保不会提供恶意内容。

language：废弃。提供语言环境。

src 可选，执行代码外部文件。

type  

模式。代替language。一般为application/x-javascript。如果type为module，可用于Es6模块导入导出。

## 位置

一般来说，`<script></scrpit>` 标签都在body中。如果在head中，则要先处理js文件，才会渲染页面。

或者使用defer，让js的下载与解析放在最后面。


## 动态加载

js除了在html中引入，还可以使用js动态加载。类似：

```
let script = document.createElement('script'); 
script.src = 'min.js'; 
document.body.appendChild(script);
```

总结:

在不使用async与defer的情况下，`script`标签会依次执行。

async异步但是不能保证执行顺序，加载好了立马执行。

defer等待页面渲染之后，才会去下载解析。

noscript 用noscript包裹的元素将不会渲染。