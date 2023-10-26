---
title: js高级程序设计-函数与闭包
date: 2020-05-04 18:25:16
tags:
    - js
categories: [学习,JS]
---

每个函数都是Function类型的实例
<!-- more -->
```
function age(){

}
// 等同于
let age = function(){
}

```
## 箭头函数

没有this指向 不能使用arguments、super ,没有prototype属性,也不能作为构造函数
```
let arrowSum = (a, b) => { 
 return a + b; 
};
function foo() { 
 console.log(arguments[0]); 
} 
foo(5); // 5 
let bar = () => { 
 console.log(arguments[0]); 
}; 
bar(5); // ReferenceError: arguments is not defined
```

## 默认参数
```
function makeKing(name = 'sky', numerals = 'miku') { 
 return `King ${sky} ${miku}`; 
} 
console.log(makeKing()); // King sky miku
```

## this

this指向如果不用call aplly bind改变,则为指向上下文对象

```
window.color = 'red'; 
let o = { 
 color: 'blue' 
}; 
function sayColor() { 
 console.log(this.color); 
} 
sayColor(); // 'red' 
o.sayColor = sayColor; 
o.sayColor(); // 'blue'

sayColor.call(this); // red 
sayColor.call(window); // red 
sayColor.call(o); // blue
//call 与aplly 传参数不同,call逗号隔开,apply用[],bind则不会立即调用
```
## 闭包
```
function createComparisonFunction(propertyName) { 
 return function(object1, object2) { 
    let value1 = object1[propertyName]; 
    let value2 = object2[propertyName]; 
    if (value1 < value2) { 
    return -1; 
    } else if (value1 > value2) { 
    return 1; 
    } else { 
    return 0; 
    } 
    }; 
}
//释放内存
let compareNames = createComparisonFunction('name'); 
// 调用函数
let result = compareNames({ name: 'Nicholas' }, { name: 'Matt' });
// 解除对函数的引用，这样就可以释放内存了
compareNames = null;
```

