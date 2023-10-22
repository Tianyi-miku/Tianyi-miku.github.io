---
title: js高级程序设计-基本引用类型
date: 2020-04-28 19:57:24
tags:
    - js
categories: [学习,JS]
---
## 对象
<!-- more -->
创建对象
```
//new Object
let person = new Object()
person.name = 'skymiku'
person.age = 28

//字面量创建
let person1 = {
    name:'skymiku',
    age:28
}

//读取
console.log(person["name"]); // "skymiku" 
console.log(person.name); // "skymiku"
```
## 数组
Array
```
let arr = new Array()
let names = [];

//转换 Array.from() 类数组, Array.of 转换为实例
console.log(Array.from("Matt")); // ["M", "a", "t", "t"]
console.log(Array.of(1, 2, 3, 4)); // [1, 2, 3, 4] 
console.log(Array.of(undefined)); // [undefined]

//判断类型
if (value instanceof Array){ 
 // 操作数组
}
if (Array.isArray(value)){ 
 // 操作数组
}

//迭代方法 keys() values() entries()
const aKeys = Array.from(a.keys()); 
const aValues = Array.from(a.values()); 
const aEntries = Array.from(a.entries()); 
console.log(aKeys); // [0, 1, 2, 3] 
console.log(aValues); // ["foo", "bar", "baz", "qux"] 
console.log(aEntries); // [[0, "foo"], [1, "bar"], [2, "baz"], [3, "qux"]]

//复制和填充 copyWithin() fill()
let  ints = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
ints.copyWithin(5); // [0, 1, 2, 3, 4, 0, 1, 2, 3, 4]

const zeroes = [0, 0, 0, 0, 0]; // 用 5 填充整个数组
zeroes.fill(5); 
console.log(zeroes); // [5, 5, 5, 5, 5]


//toString()
let colors = ["red", "blue", "green"];
colors.toString() //"red", "blue", "green"

//栈方法 push() pop() 最后加入,最后弹出
colors.push('red')
colors.pop()
//队列  push() shift() 最后加入,最先弹出
colors.push('red')
colors.shift()
// 排序 sort与reverse反序
let values = [1, 2, 3, 4, 5]; 
values.reverse(); 
alert(values); // 5,4,3,2,1
let values = [0, 1, 5, 10, 15]; 
values.sort(); 
alert(values); // 0,1,10,15,5

//数组常用方法
//concat 链接数组 slice切割数组, splice删除或者替换新增
//indexOf lastIndexOf includes 判断元素是否在其中
//find findIndex 寻找某个元素
//every 每一项都满足
//filter 过滤
//forEach 循环
//map 返回想要的数据格式
//some 某项满足
//reduce 并归,会将上一次的结果作为第一个参数传入,通常用于累加或累减
//reduceRight 从后面开始到第一项
```
ArrayBuffer,DataView了解
```
ArrayBuffer()普通的 JavaScript 构造函数，可用于在内存中分配特定数量的字节空间。
const buf = new ArrayBuffer(16); // 在内存中分配 16 字节
alert(buf.byteLength); // 16

const fullDataView = new DataView(buf);
```
## Map WeakMap set WeakSet
Map键值对
```
const m = new Map();
const m1 = new Map([ 
 ["key1", "val1"], 
 ["key2", "val2"], 
 ["key3", "val3"] 
]);
m1.has('key1') //true 判断是否存在
m1.set("key4", "val4") //添加
m1.delete("key4") //删除
m1.clear()//清除

//迭代
for (let pair of m1.entries()) { 
 alert(pair); 
 // [key1,val1] 
// [key2,val2] 
// [key3,val3]
} 
m1.forEach((val, key) => alert(`${key} -> ${val}`));
``` 

## WeakMap

弱映射中的键只能是 Object 或者继承自 Object 的类型
```
const key1 = {id: 1}, 
 key2 = {id: 2}, 
 key3 = {id: 3}; 
// 使用嵌套数组初始化弱映射
const wm1 = new WeakMap([ 
 [key1, "val1"], 
 [key2, "val2"], 
 [key3, "val3"] 
]);

// 方法包括get()和 has(),delete()
const wm = new WeakMap(); 
const loginButton = document.querySelector('#login'); 
// 给这个节点关联一些元数据
wm.set(loginButton, {disabled: true});//当节点从 DOM 树中被删除后，垃圾回收程序就可以立即释放其内存
```

## Set
Set 在很多方面都像是加强的 Map
```
const s = new Set(); 
s.add('foo'); 
alert(s.size); // 1 
s.add('foo'); 
alert(s.size); // 1

//方法 用add()增加值,has()查寻,size数量,delete,clear 删除,keys(),values()拿值
```

## WeakSet

弱引用与weakMap相同,没有clear,不能添加相同key
```
const ws = new WeakSet()
const val1 = {id: 1}, 
 val2 = {id: 2}, 
 val3 = {id: 3}; // 使用数组初始化弱集合
const ws1 = new WeakSet([val1, val2, val3]); 
alert(ws1.has(val1)); // true 
alert(ws1.has(val2)); // true 
alert(ws1.has(val3)); // true
```

总结:
Object 所有引用类型都是从它继承
Array 数组 
定型数组包含一套不同的引用类型，用于管理数值在内存中的类型
Map、WeakMap、Set 和 WeakSet 
set去重
Weak弱引用,随时可删除,没有clear方法,随时可回收,键为对象
