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

## undefined

声明的变量，没有赋值的，相当于赋值undefined。

```
let name 

console.log(name) //undefined
```

函数renturn回来的空值也为undefined。

```
const a = [1,2]
const b = a.map(i => {
    if(i !== 1){
        return i
    }
})
console.log(b) // [undefined,2]
```

## null

null 只有一个值，逻辑上说表示空对象指针。
```
let car = null; 
console.log(typeof car); // "object"
```

## Boolean

布尔值，有两个值 true与false

字符串与Boolean转换

```
let message = "Hello world!"; 
Boolean(message);  //true

let msg = ''
Boolean(message); //false
```

数字与Boolean
```
Boolean(1) //true
Boolean(0) //false
Boolean(NAN) //false
```
Object与Boolean

```
Boolean({name: 1}) //对象始终为true
```
null与Boolean undefined

```
Boolean(null) //false

Boolean(undefined) //false
```

## Number 

Number 包括整数，浮点数 NAN

浮点数必须有小数点，并且小数点后面有数值。JS中，由于内存限制，有最大值最小值限制。如果超过了，则有-Infinity（负无穷大） Infinity(正无穷大)表示。

NAN则表示不是数字。

```
console.log(0 / 0) //NAN
console.log(5/0); //Infinity
console.log(-5/0) //-Infinity

console.log(NaN==NaN) //false
```
用isNaN可以判断是不是数字，如果可以转换为数字则返回ture,其余返回false。

数值转换 Number parseInt parseFlaot

Number
```
let num1 = Number("Hello world!"); // NaN 
let num2 = Number(""); // 0 
let num3 = Number("000011"); // 11 
let num4 = Number(true);
```
ParseInt
```
let num1 = parseInt("1234blue"); // 1234 
let num2 = parseInt(""); // NaN 
let num3 = parseInt("0xA"); // 10，解释为十六进制整数
let num4 = parseInt(22.5); // 22 
let num5 = parseInt("70"); // 70，解释为十进制值
let num6 = parseInt("0xf"); // 15，解释为十六进制整数
```
parseFloat

```
let num1 = parseFloat("1234blue"); // 1234，按整数解析
let num2 = parseFloat("0xA"); // 0 
let num3 = parseFloat("22.5"); // 22.5 
let num4 = parseFloat("22.34.5"); // 22.34 
let num5 = parseFloat("0908.5"); // 908.5 
let num6 = parseFloat("3.125e7"); // 31250000

//在平时开发中，常常会用到toFixed(1)来保留小数
```
## string

字符串，用单引号双引号反引号表示。

```
let lang = "Java"; 
lang = lang + "Script";
```
转换方法toString()

```
let age = 11; 
let ageAsString = age.toString(); // "11" 
let found = true;
let foundAsString = found.toString();  // "true" 
```
如果不确定是null或者undefined 

```
String(null) //'null'
String(undefined ) //'undefined'
```

## Symbol

独一无二的，不可变

```
let name1 = Symbol('1'); 
let name2 = Symbol('1');
console.log(name1 == name2) //false
```
symbol也有很多方法，用到的时候自行查看。

## 复杂类型

Object 对象。

```
let o = new Object();
cosole.log(o.constructor)

hasOwnProperty //是否有属性
isPrototypeOf  //是否另一个对象的原型
propertyIsEnumerable //用于判断给定的属性是否可以使用
toLocaleString //返回字符串，语言本地环境
toString //返回字符串
valueOf //返回对象对应的字符串、数值或布尔值表示，通常与 toString()的返回值相同

```