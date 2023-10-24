---
title: js高级程序设计-对象,类与面向对象
date: 2020-05-2 18:37:04
tags:
    - js
categories: [学习,JS]
---
## 面向对象

面向对象编程,围绕具体的对象进行可抽象化的描述,还有面向过程,主要是通过过程描述事件
<!-- more -->
对象
```
let person = new Object(); 
person.name = "skymiku";

let peson1={
    name: "skymiku"
}
```
属性

[[Configurable]] 表示属性是否可以删除

[[Enumerable]] 表示属性是否可以通过for in循环列举

[[Writable]] 表示属性是否可以被修改

[[Value]] 属性的值

[[Get]]：获取函数

[[Set]]：设置函数

```
Object.definePorperty(person,"name",{
     writable: false,
     value: 'skymiku1'
})

Object.defineProperty(person, "name", { 
 get() { 
 return this.name; 
 }, 
 set(newValue) { 
 this.name = newValue; 
 } 
});
//vue2中属性Set get劫持,就是用的这个
```

Object.getOwnPropertyDescriptors //获得属性描述,是否可读,修改

## 合并

```
dest = {}; 
src = { a: {} }; 
Object.assign(dest, src);
//浅拷贝
```

## 解构

```
// 使用对象解构
let person = { 
 name: 'skymiku', 
 age: 27 
}; 
let { name, age } = person;
```

## 工厂模式

接受参数,返回对象
```
function createPerson(name, age, job) { 
 let o = new Object(); 
 o.name = name; 
 o.age = age; 
 o.job = job; 
 o.sayName = function() { 
 console.log(this.name); 
 }; 
 return o; 
}
```
## 构造函数
没有显式创建函数,内部有this指向,没有retrun
```
function Person(name, age, job){ 
 this.name = name; 
 this.age = age; 
 this.job = job; 
 this.sayName = function() { 
 console.log(this.name); 
 }; 
} 
let person1 = new Person("Nicholas", 29, "Software Engineer");
```

## 原型模式

每个函数都有prototype属性,对象中包含所有共享的属性和方法
```
function Person() {} 
Person.prototype.name = "Nicholas"; 
Person.prototype.age = 29;
```
只要创建一个函数,就会默认创建一个prototype指向它的原型对象,所有原型对象自动获得一个constructor属性,指回关联的构造函数

在大多数浏览中,会暴露_proto_属性,访问对象原型

```
console.log(Person.prototype.__proto__ === Object.prototype); // true 
console.log(Person.prototype.__proto__.constructor === Object); // true 
console.log(Person.prototype.__proto__.__proto__ === null); // true
```

## 对象属性

for-in 循环、Object.keys()、Object.getOwnPropertyNames()、Object.getOwnPropertySymbols() Object.values()和 Object.entries


## 原型链

每个构造函数都有一个原型对象,prototype指向原型对象,原型对象中有一个constructor指回构造函数

所有引用类型都继承自Object,任意函数默认原型都是Object实例,寻找属性会沿着原型链一值找,直到null

## 继承

包括组合继承,寄生式继承,寄生式组合继承,原型继承,extend 

组合继承使用原型链继承原有的方法和属性,又可以让实例有自己的属性

原型继承
```
let person = { 
 name: "Nicholas", 
 friends: ["Shelby", "Court", "Van"] 
}; 
let anotherPerson = Object.create(person, { 
 name: { 
 value: "Greg" 
 } 
}); 
console.log(anotherPerson.name); // "Greg"
```

## 类

类是特殊的构造函数,super可以改变内部this指向

```
// 类声明
class Person {}
```

总结: 继承主要靠原型链
     构造函数的原型赋值为另一个类型的实例
     对象中的prototype共用