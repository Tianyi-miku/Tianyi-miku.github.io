---
title: typescript基础使用
date: 2023-11-15 18:21:50
tags:
    - typescript
categories: [学习,typescript]
---

总结typescript基础使用
<!-- more -->
# 接口
```
interface LabelledValue {
  label: string;
  [index: string]: number; //索引
}
```
# 类
```
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

let greeter = new Greeter("world");
```
# 函数
```
function add(x: number, y: number): number {
    return x + y;
}

let myAdd = function(x: number, y: number): number { return x + y; };
```
# 泛型
```
function identity<T>(arg: T): T {
    return arg;
}
```
# namespace 
```
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
}
```

## tsconfig.json

[详情](https://www.tslang.cn/docs/handbook/compiler-options.html)
```
{
    "compilerOptions": {
        "module": "commonjs",  //编码方式
        "typeRoots" : ["./typings"] //指定"types": []来禁用自动引入@types包。
        ....
    },
    "files": [ //路径
    ]
}

```