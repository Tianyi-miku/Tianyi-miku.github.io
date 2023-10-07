---
title: vue2虚拟dom
date: 2022-06-2 13:16:34
tags:
    - vue2
    - 源码
categories: 学习
---

## 模板编译，构建ast树

首先，有模板拿模板，没有模板，拿到页面dom元素。

通过正则，去匹配页面标签和元素，如： <div></div>之类的。

将template转换为Ast树。

匹配元素的时候，一边匹配，一边删除,文本，标签，不同的用不同的正则匹配。解析到的时候，将其属性挂载在上面。


通过父元素存入栈第一个，然后循环子元素入栈出栈,更新最后一个出栈元素,从而构建出ast树。


## 通过ast树生成字符串

Ast树通过代码去拼接字符串代码，字符串中的变量，通过正则去匹配，匹配成功然后放入字符串中。

这一步主要是拼接成想要的字符串。生成类似：

```
_c('div',{id:"app"} _v..._s..)
```

## 生成render with + new function

生成好的字符串，通过 with function 实现取值。模板编译原理都是通过with +new function来实现的。

```
code = `with(this){return `${code}`}`
let render = new Function(code)

```

## 创建虚拟节点

通过判断标签，文本等调用不同函数调用creatVNode ,创建虚拟Dom节点。

调用render 执行取值会触发get 方法！

ast 描述的是语法本身，可以描述html css js 

虚拟Dom描述的是Dom 元素，可以自定义属性！



## 渲染 update

patch 通过diff算法去新建或者更新真实Dom！

nodeType 判断是否真实dom，如果没有真实dom先新建 creatElm()。

有的话通过diff算法更新。


## 流程

创建响应式数据 =》 模板转换为Ast语法树 =》 Ast语法树生成render函数 （编译） =》 数据更新之后只渲染render函数 

Render会产生虚拟节点 （定位响应式数据） =》 虚拟dom 生成真实dom