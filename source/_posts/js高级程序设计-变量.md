---
title: js高级程序设计-js变量声明
date: 2020-04-23 16:36:41
tags:
    - js
categories: [学习,JS]
---

## 变量声明

js 中声明变量方法包括 var let const 
<!-- more -->

# var

```
var message = 'hi'
```

作用域可提升。但局部作用域中，退出将会销毁。

```
function foo() { 
 console.log(age); 
 var age = 26; 
} 
foo(); // undefined

```
局部作用域
```
function test() { 
 var message = "hi"; // 局部变量
} 
test(); 
console.log(message); //报错
```

# let

let 为块级作用域，不允许重复声明。

```
if (true) { 
 let age = 26; 
 console.log(age); // 26 
} 
console.log(age); //age 没有定义
```

# const

const 同样为块级作用域，不允许重复声明。与let相比，会暂时性死区，

```
const message = 'hi'

message = 'hello' // TypeError

```

const声明的基础变量不能修改，但是复杂类型的可以修改其属性，因为内存中存放的是指针。

```
const singger = {
    name: 'skymiku',
    age: '24'
}

singger.age = '25'
```

对于未声明的变量，只能调用typeof。在严格模式下,delete未声明的变量也会报错。