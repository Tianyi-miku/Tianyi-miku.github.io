---
title: js高级程序设计-变量,作用域
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
