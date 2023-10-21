---
title: js高级程序设计-基本引用类型
date: 2020-04-27 20:03:54
tags:
    - js
categories: [学习,JS]
---

引用类型为堆结构存储,复制其引用的时候,复制的是它的指针
<!-- more -->
## 对象
```
let data = new Date()
//Date()在这里就是构造函数，创建一个只有默认属性和方法的简单对象

let y2k = new Date(2000, 0);// 本地时间 2000 年 1 月 1 日零点
```

Date 常用方法
```
new Date().getTime() //毫秒
new Date().getFullYear() //年
new Date().getMonth() //月
new Date().getDate() //日
.....
```
当然项目中一般会用到其他插件来快速格式化,类似day.js 或者moment.js


RegExp 常用
```
/g 全局匹配 /i 忽略大小写 /m 多行 
let pattern1 = /at/g; /g查看所有at
let pattern2 = /[bc]at/i;// 匹配第一个"bat"或"cat"，忽略大小写
let pattern2 = /\[bc\]at/i;// 匹配第一个"[bc]at"，忽略大小写

let text = "this has been a short summer"; 
let pattern = /(.)hort/g; 
if (pattern.test(text)) { 
 console.log(RegExp.input); // this has been a short summer 
 console.log(RegExp.leftContext); // this has been a 
 console.log(RegExp.rightContext); // summer 
 console.log(RegExp.lastMatch); // short 
 console.log(RegExp.lastParen); // s 
}
.....
```

boolean对象
```
let falseObject = new Boolean(false); 
let result = falseObject && true; 
console.log(result); // true 
let falseValue = false; 
result = falseValue && true; 
console.log(result); // false
```

number对象
```
let numberObject = new Number(10);
typeof numberObject //'object'
numberObject.toString() //'10'
```

string 常用
```
let stringObject = new String("hello world");
let stringValue = "hello world"; 
console.log(stringValue.length); // "11"
let stringValue = "hello "; 
let result = stringValue.concat("world"); 
console.log(result); // "hello world"  拼接,数组拼接也可以用这个
console.log(stringValue); // "hello"

//slice substring substr区别不大,只是切割起始点不同,具体用到的时候查看

//indexOf()和 lastIndexOf() 
let stringValue = "hello world"; 
console.log(stringValue.indexOf("o")); // 4 
console.log(stringValue.lastIndexOf("o")); // 7
console.log(stringValue.indexOf("o", 6)); // 7 
console.log(stringValue.lastIndexOf("o", 6)); // 4

//es6 新增startsWith()、endsWith()和 includes()。项目中includes用的比较多,也可以用到数组
//trim() //删除所有空格

//repeat 复制
let stringValue = "na "; 
console.log(stringValue.repeat(16) + "batman"); 
// na na na na na na na na na na na na na na na na batman

//padStart与padEnd 与数组fill方法类似
let stringValue = "foo"; 
console.log(stringValue.padStart(6)); // " foo" 
console.log(stringValue.padStart(9, ".")); // "......foo" 
console.log(stringValue.padEnd(6)); // "foo " 
console.log(stringValue.padEnd(9, ".")); // "foo......"

//转换大小写 toLowerCase()、toLocaleLowerCase() toUpperCase()和toLocaleUpperCase()
let stringValue = "hello world"; 
console.log(stringValue.toLocaleUpperCase()); // "HELLO WORLD" 
console.log(stringValue.toUpperCase()); // "HELLO WORLD" 
console.log(stringValue.toLocaleLowerCase()); // "hello world" 
console.log(stringValue.toLowerCase()); // "hello world

//match 正则匹配
let text = "cat, bat, sat, fat"; 
let pos = text.search(/at/); 
console.log(pos); // 1
......
```