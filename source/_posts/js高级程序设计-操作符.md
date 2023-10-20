---
title: js高级程序设计-操作符
date: 2020-04-25 20:16:28
tags:
    - js
categories: [学习,JS]
---
一般来说用的比较多的操作符 + - * / || && 
<!-- more -->
自增与自减
```
let age = 28;
++age; //29 等于age = age + 1

--age; //28 等于age = age - 1
```
转换
```
let n = '1'
let f = '1.1'
let b = false
let x = 'x'

let xs = 1

n = +n //1
f = +f //1.1
b = +b //0
x = +x //NaN

xs = -xs //-1 减号与加号一致，只是转换为负数。

```

与、非、或者
```
console.log(!false); // true
console.log(!false && true); // true
console.log(!false || false);//false
```

乘除
```
let b = 2 * 2 //4 
let a = 4 /2  //2
```
如果其中一个数字超过内存为Infinity或者-Infinity，则结果为NaN，如果是 0 除以 0，则返回 NaN，非 0 的有限值除以 0，返回Infinity或者 -Infinity

取模
```
let b = 26 % 5 //1
```

指数

```
let a = 3 ** 2 //9

let squared = 3; 
squared **= 2; 
console.log(squared); //9 **=为赋值。
```

等于，两个等号会转换，三个等号要数据类型也相等。
```
console.log(null == undefined) //true
console.log(null === undefined) //false
console.log("NaN" == NaN)  //NaN与NaN永远不相等
let result1 = ("55" != 55); // false，转换后相等
let result2 = ("55" !== 55); // true，不相等，因为数据类型不同
```

if else, do while 
```
if (i > 25) { 
 console.log("Greater than 25."); 
} else{

}

let i = 0; 
do { 
 i += 2; 
} while (i < 10) //小于 10，循环就会重复执行
```

for 与 for in ,for of
```
for(let i=0 ,i<10 ,i ++){

}

//for in开发中主要用于Object遍历属性，如果用于数组，将会将其所有的可枚举属性遍历，包括原型链上可遍历的。
var obj = {a:1, b:2, c:3}
    
for (let key in obj) {
  console.log(key)
}

//for of主要用于数组遍历
const array1 = ['a', 'b', 'c']
 
for (const val of array1) {
  console.log(val)
}
```

## 常用语句

break 跳出循环 ，continue 跳出本次循环。
```
var obj = {a:1, b:2, c:3}
    
for (let key in obj) {
    if(key === 'a'){
        break; //跳出循环，continue跳出本次循环继续下一次循环.
    }
  console.log(key)
}

```

switch case 判断分支语句。
```
switch (i) { 
 case 25: 
 console.log("25"); 
 break; 
 case 35: 
 console.log("35"); 
 break; 
 default: 
 console.log("Other"); 
}
```

