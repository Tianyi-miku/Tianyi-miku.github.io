---
title: js高级程序设计-变量,作用域,内存
date: 2020-04-26 21:13:24
tags:
    - js
categories: [学习,JS]
---

变量包含两种,初始值与引用值。
<!-- more -->

基本应用类型 string null undefined boolean number Symbol。 引用类型 Object Array Function 

```
let num1 = 5; 
let num2 = num1; //复制值,两个不同的内存地址。

let obj1 = new Object(); 
let obj2 = obj1; 
obj1.name = "Nicholas"; 
console.log(obj2.name); // "Nicholas" //引用值,两个指向的同一个内存地址,复制值只是将指针复制。
```

判断值类型,基本类型用typeOf,复杂用instanceof,由原型链决定
```
let s = "Nicholas"; 
let b = true;
console.log(typeof s); // string
console.log(typeof b); // boolean

console.log(person instanceof Object); // 变量 person 是 Object 吗？
console.log(colors instanceof Array); // 变量 colors 是 Array 吗？
```

作用域包括局部与全局,在程序中,上下文可以通过作用域链访问外部的属性

```
var color = "blue";  //全局
function changeColor() { 
    let Mycolor = "red" //局部
    console.log(color) //blue
} 

```
## 作用域链增强

try/catch语句中的catch与with

```
//catch会创建一个新的变量对象,包含抛出的错误对象的声明
//with 则指定一个对象作为作用域前端

function buildUrl() { 
 let qs = "?debug=true"; 
 with(location){ 
 let url = href + qs; 
 } 
 return url; 
}
```

vue中模板编译的时候,就用过with function来更方便取值


## 垃圾回收

1.标记清理

指的是变量进入上下文中,变量会加上存在上下文的标记,当离开上下文后,会被加上离开上下文的标记

垃圾回收的时候,会标记所有变量,然后将所有用到的或者引用的变量标记去掉,此后再加入的都是待删除的,最后内存清理,销毁带标记的值.

2.引用计数

声明变量并引用值时候,标记值的引用为1,如果又赋值给另一个变量,标记的引用+1,当引用值被覆盖的时候,标记的引用值-1,直到引用值为0的时候,垃圾回收下次运行的时候将会释放0的内存

但是因为引用计数存在循环引用,和相互引用导致引用计数永远不为0,所以后来很少用到这个回收


垃圾回收程序会周期的运行,如果有很多变量,可能造成性能问题

## 内存管理

将内存维持在一个小的值可以让页面性能更好

```
function createPerson(name){ 
 let localPerson = new Object(); 
 localPerson.name = name; 
 return localPerson; 
} 
let globalPerson = createPerson("Nicholas"); 
// 解除 globalPerson 对值的引用
globalPerson = null;
```
设置值为null,可以释放其引用,下次垃圾回收的时候将会被回收

当然在现代的框架中,大多数时候不需要自己去手动回收,像是Vue,离开页面的时候将自动销毁其引用,下次垃圾回收的时候会被回收

在代码中,使用let const有助于改进垃圾回收,因为块级作用域通常比全局更早终止


## 内存泄漏

1. 意外的全局变量,挂载在window上,一旦创建将不会被销毁
```
function setName() { 
 name = 'Jake'; 
}
```
2. 定时器
```
let name = 'Jake'; 
setInterval(() => { 
 console.log(name);  //包含外部变量,将不会被回收
}, 100);
```
3. 闭包
```
let outer = function() {  //调用此函数返回一个变量,只要此变量存在,name将一直存在
 let name = 'Jake'; 
 return function() { 
 return name; 
 }; 
};
```

## 静态分配与对象池

这一般很少用到,只需要了解即可

如果创建对象,立马又修改对象再返回调用者,它的生命周期很短,很快失去引用,而且被频繁调用,则会更频繁垃圾回收,比如
```
function addVector(a, b) { 
 let resultant = new Vector(); 
 resultant.x = a.x + b.x; 
 resultant.y = a.y + b.y; 
 return resultant; 
}
```
这时候可以修改函数,使用一个已经知道的对象
```
function addVector(a, b, resultant) { 
 resultant.x = a.x + b.x; 
 resultant.y = a.y + b.y; 
 return resultant; 
}
```
或则伪对象池
```
// vectorPool 是已有的对象池 
let v1 = vectorPool.allocate(); 
let v2 = vectorPool.allocate(); 
let v3 = vectorPool.allocate(); 
v1.x = 10; 
v1.y = 5; 
v2.x = -3; 
v2.y = -6; 
addVector(v1, v2, v3); 
console.log([v3.x, v3.y]); // [7, -1] 
vectorPool.free(v1); 
vectorPool.free(v2); 
vectorPool.free(v3); 
// 如果对象有属性引用了其他对象
// 则这里也需要把这些属性设置为 null 
v1 = null; 
v2 = null; 
v3 = null;
```
使用数组维护例子
```
let vectorList = new Array(100); 
let vector = new Vector(); 
vectorList.push(vector);
```

总结:

原始值为6个基本数据类型,String number null undefined symbol boolean

基本数据类型大小固定,保存在栈中,复制时候将会产生第二个副本

而对象储存在堆内存中,引用值只是对象的指针,复制的时候,只是复制的指针

垃圾回收机制是自动运行,离开作用域的值将会标记成可回收,下一次垃圾回收的时候将会删除

主流的垃圾清理机制是标记清理,给离开作用域的值加上标记,下次回收,计数清理则要记录值被引用多少次,直到引用次数为0的时候才会被回收