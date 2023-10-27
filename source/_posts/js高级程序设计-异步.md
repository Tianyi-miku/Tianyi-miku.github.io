---
title: js高级程序设计-异步
date: 2020-05-05 14:13:49
tags:
    - js
categories: [学习,JS]
---
在之前,异步都是定义了回调函数,来表明异步,一旦复杂了,就会形成回调地狱
<!-- more -->
```
function double(value, callback) { 
 setTimeout(() => callback(value * 2), 1000); 
} 
double(3, (x) => console.log(`I was given: ${x}`));
```
## Promise

Promise 三个状态,pedding fulfilled rejected
```
let p = new Promise(() => {}); 
setTimeout(console.log, 0, p);
```

## async await

```
function randomDelay(num) {
 setTimeout(() => { 
 console.log(111); 
 }, 1000)
}

async function foo() { 
 const t0 = Date.now(); 
 await randomDelay(0);
 }
```