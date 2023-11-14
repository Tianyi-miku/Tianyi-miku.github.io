---
title: typescript基础使用
date: 2023-11-14 19:44:10
tags:
    - typescript
categories: [学习,typescript]
---

总结typescript基础使用
<!-- more -->
# 布尔值
```
let a : boolean = false;
```
# 数字
```
let a :number = 1;
let b :number = 1.0;
```
# 字符串
```
let a :string = 'string';
```
# 数组
```
let list:[] = [1];
let arr: Array<number> = [1];
```
# 元组
```
let a : [string, number] = ['string', 1];
```
# 枚举
```
enum Color {
    red,
    green,
    bule
}
let a: Color = Color.bule;
```
# any
```
let notSure: any = 4;
```
# void 没有返回值
```
function warnUser(): void {
    console.log("This is my warning message");
}
```
# Null 和 Undefined
没什么太大用
```
let a:null = null;
let b:undefined = undefined;
```
# never 
 返回never的函数必须存在无法达到的终点
```
function infiniteLoop(): never {
    while (true) {
    }
}
 ```
 # object 
 非基础类型
 ```
 declare function create(o: object | null): void;
create({ prop: 0 }); // OK
 ```

 ## 断言
 ```
 let strLength: number = (<string>someValue).length;
 let strLength: number = (someValue as string).length;
 ```
 