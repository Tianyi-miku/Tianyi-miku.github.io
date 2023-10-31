---
title: js高级程序设计-事件
date: 2020-05-08 15:21:55
tags:
    - js
categories: [学习,JS]
---
## Event

事件冒泡: 从下到上,具体的某一个元素绑定到最上面窗口
事件捕获: 从上到下
<!-- more -->

addEventListener,removeEventListener
```
let btn = document.getElementById("myBtn"); 
btn.onclick = function() { 
 console.log("Clicked"); 
};
btn.onclick = null; // 移除事件处理程序

btn.addEventListener("click", (event) => { 
 console.log(event); //事件对象
 event.preventDefault(); //取消默认事件
  event.stopPropagation(); //立即阻止事件冒泡
}, false);

btn.removeEventListener("click");
```

EventUtil 可阻止跳转,或者传递event

# 界面事件
DOMActivate //废弃
load //加载完成触发
unload//卸载
abort //<object>元素提前终止下载触发
error //报错触发
select //在文本框选择时触发
resize //缩放触发
scroll //滚动触发

beforeunload//阻止页面关闭
DOMContentLoaded //DOm加载完毕

hashchange//url改变事件
```
window.addEventListener("hashchange", (event) => { 
 console.log(`Old URL: ${event.oldURL}, New URL: ${event.newURL}`); 
})
```

# 焦点

blur //失去焦点
focus //触发焦点
focuscout DOMFocusOut// 在失去焦点的元素上触发。
focusin DOMFocusIn//在获得焦点的元素上触发。

# 鼠标
click //点击
dblclick //双击
mousedown //按下鼠标
mouseenter//移入
mouseleave //移出
mousemove// 元素上移动
mouseout //元素上移出
mouseover //元素外部移入内部
mouseup //释放鼠标
mousewheel //滚轮

# 键盘
keydown 案件触发 keypress一样
keyup 释放
```
let textbox = document.getElementById("myText"); 
textbox.addEventListener("keyup", (event) => { 
 console.log(event.keyCode); 
});
```

# 手机事件
orientationchange //横屏竖屏
deviceorientation// 手机旋转
devicemotion //移动

touchstart //手指放入
touchmove //手指移动
touchend //移开
touchcancel //停止跟踪

touches//触点
......

# 事件委托

将所有事件委托到document上

# 删除事件

removeChild() replaceChild() innerHTML
删除之前,最好手工删除它的事件处理程序,不然可能导致还存留内存中

# 事件模拟