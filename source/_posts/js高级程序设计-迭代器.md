---
title: js高级程序设计-基本引用类型
date: 2020-05-1 18:21:23
tags:
    - js
categories: [学习,JS]
---
JS中最简单的迭代器就是循环
```
for (let i = 1; i <= 10; ++i) { 
 console.log(i); 
}
```
<!-- more -->

## Iterable

内置Iterable的有 string Array map set arguments DOM 集合等
```
let num = 1; 
let obj = {}; 
// 这两种类型没有实现迭代器工厂函数
console.log(num[Symbol.iterator]); // undefined 
console.log(obj[Symbol.iterator]); // undefined 
let str = 'abc'; 
let arr = ['a', 'b', 'c']; 
let map = new Map().set('a', 1).set('b', 2).set('c', 3); 
let set = new Set().add('a').add('b').add('c'); 
let els = document.querySelectorAll('div'); 
// 这些类型都实现了迭代器工厂函数
console.log(str[Symbol.iterator]); // f values() { [native code] } 
console.log(arr[Symbol.iterator]); // f values() { [native code] } 
console.log(map[Symbol.iterator]); // f values() { [native code] } 
console.log(set[Symbol.iterator]); // f values() { [native code] } 
console.log(els[Symbol.iterator]); // f values() { [native code] }
```
接受可迭代对象的有,for of,数组解构,扩展操作符,Array.from,创建集合,创建映射,Promise.all Promise.race,yeild*

```
let arr = ['foo', 'bar', 'baz']; 
// for-of 循环
for (let el of arr) { 
 console.log(el); 
}
let [a, b, c] = arr; // 数组解构
console.log(a, b, c); // foo, bar, baz  // 扩展操作符
let arr2 = [...arr]; // ['foo', 'bar', 'baz'] 
let arr3 = Array.from(arr); // ['foo', 'bar', 'baz'] 
let set = new Set(arr); // Set(3) {'foo', 'bar', 'baz'} 
let pairs = arr.map((x, i) => [x, i]);  [['foo', 0], ['bar', 1], ['baz', 2]] 
let map = new Map(pairs); // Map(3) { 'foo'=>0, 'bar'=>1, 'baz'=>2 }
```

## 迭代器协议

一次性对象,用来关联可迭代对象,调用next()方法返回一个IteratorResult 对象,包含下一个值,其中包含两个属性：done 和 value。done是false或者undefined为耗尽
```
let arr = ['foo', 'bar']; // 可迭代对象
console.log(arr[Symbol.iterator]); // f values() { [native code] }// 迭代器工厂函数
let iter = arr[Symbol.iterator](); 
console.log(iter); // ArrayIterator {}
// 执行迭代
console.log(iter.next()); // { done: false, value: 'foo' } 
console.log(iter.next()); // { done: false, value: 'bar' } 
console.log(iter.next()); // { done: true, value: undefined }
```

## 自定义迭代器
```
class Counter { 
 constructor(limit) { 
 this.limit = limit; 
 } 
 [Symbol.iterator]() { 
 let count = 1, 
 limit = this.limit; 
 return { 
 next() { 
 if (count <= limit) { 
 return { done: false, value: count++ }; 
 } else { 
 return { done: true, value: undefined }; 
 } 
 } 
 }; 
 } 
} 
let counter = new Counter(3);
for (let i of counter) { console.log(i); } // 1 2 3
```

提前终止return(),for-of 循环通过 break、continue、return 或 throw 或者解构操作并未消费所有值


## 生成器
```
function* generatorFn() {}
```
调用生成器方法,会产生一个生成器对象,处于暂停执行状态,调用next(),恢复执行 ,通过yield,也可以中断执行
```
function* generatorFn() {} 
let generatorObject = generatorFn(); 
console.log(generatorObject); // generatorFn {<suspended>} 
console.log(generatorObject.next()); // { done: true, value: undefined }
```

总结:

迭代器是可以以任意对象实现的接口,可以循环获取对象的值 调用next后,返回done与value,done表示后面还有没有更多值访问,next执行下一个

调用生成器,返回一个生成器对象,yeild可以暂停,next可以恢复,加上*后,可以序列化一系列值