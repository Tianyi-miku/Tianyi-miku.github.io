---
title: js高级程序设计-window对象
date: 2020-05-07 19:11:01
tags:
    - js
categories: [学习,JS]
---
## Dom
任何 HTML 或 XML 文档都可以用 DOM 表示为一个由节点构成的层级结构
<!-- more -->

# Node
nodeType //节点的类型
nodeName nodeValue //节点的信息
appendChild() //最后添加
insertBefore() //指定位置添加
replaceChild()//替换
removeChild() //移除
cloneNode() //拷贝

childElementCount //子元素数量
firstElementChild
lastElementChild
previousElementSibling //前一个Element类型的同胞元素
nextElementSibling//后一个Element类型的同胞元素
....


# document
 document.title //标题  
 document.URL   //url
 document.domain //域名
 document.referrer// 来源
 document.getElementById() //寻找单个元素ID
 document.getElementsByTagName() //寻找多个元素ID
 document.getElementsByName() //寻找单个元素name
 document.getElementsByClassName()  //寻找class name

 document.write()
 document.writeln() //写入加换行
 document.open() document.close() //打开和关闭网页输出流
 document.querySelector()
 document.querySelectorAll()
 document.body.matches()

 document.head
 document.hasFocus() //焦点
 document.readyState()// 加载状态 loading complete
 document.compatMode()//渲染模式 标准或者混杂
 ....
 
 # Element
 ```
 <div id="myDiv" class="bd" title="Body text" lang="en" dir="ltr"></div>

 let div = document.getElementById("myDiv"); 
alert(div.tagName); // "DIV" 
alert(div.tagName == div.nodeName); // true

let div = document.getElementById("myDiv"); 
alert(div.getAttribute("id")); // "myDiv" 
alert(div.getAttribute("class")); // "bd" 
alert(div.getAttribute("title")); // "Body text" 
alert(div.getAttribute("lang")); // "en" 
alert(div.getAttribute("dir")); // "ltr"
....
 ```