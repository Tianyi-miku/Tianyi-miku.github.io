---
title: js高级程序设计-代理与反射
date: 2020-05-3 14:14:23
tags:
    - js
categories: [学习,JS]
---
## 代理Proxy
<!-- more -->
```
const target = { 
 id: 'target' 
}; 
const handler = {}; 
const proxy = new Proxy(target, handler);
// id 属性会访问同一个值
console.log(target.id); // target 
console.log(proxy.id); // target
//能通过访问代理,来获取值
```

## 拦截
```
const target = { 
 foo: 'bar' 
}; 
const handler = { 
 // 捕获器在处理程序对象中以方法名为键
 get() { 
 return 'handler override'; 
 } 
}; 
const proxy = new Proxy(target, handler);
console.log(proxy.foo); // handler override
```

vue3中的双向绑定就是通过proxy实现的

## Reflect 反射 

Reflect.set() Reflect.get()  Reflect.has()
```
const target = { 
 foo: 'bar' 
}; 
const handler = { 
 get() { 
 return Reflect.get(...arguments); 
 } 
}; 
const proxy = new Proxy(target, handler); 
console.log(proxy.foo); // bar 
console.log(target.foo); // bar
const user = { 
 name: 'Jake' 
}; 
const proxy = new Proxy(user, { 
 get(target, property, receiver) { 
 console.log(`Getting ${property}`); 
 return Reflect.get(...arguments); 
 }, 
 set(target, property, value, receiver) { 
 console.log(`Setting ${property}=${value}`); 
 return Reflect.set(...arguments); 
 } 
}); 
proxy.name; // Getting name 
proxy.age = 27; // Setting age=27
```

总结 代理可以定义拦截处理程序对象,可以拦截修改大部分JS,反射则是封装一整套拦截操作对应的方法

用代理可以做到跟踪属性访问、隐藏属性、阻止修改或删除属性、函数参数验证、构造函数参数验证、数据绑定，以及可观察对象等等