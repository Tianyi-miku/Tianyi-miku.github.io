---
title: vue2响应式数据实现
date: 2022-05-4 18:05:13
tags:
    - vue2
    - 源码
categories: [学习,笔记]
---
根据vue原理思路，重写捋一遍vue2实现。
<!-- more -->
首先 配置环境，主要用到@babel 解析，rollup打包工具配置好环境。

新建一个html文件，新建index.js，引入。再script中new一个vue。


```
   const myVue = new Vue({
            data() {
                return {
                    name: 13,
                    skymiku: {
                        age: 20,
                        name: 'skymiku'
                    }
                }
            }
        })
```

## 初始化vue

在index中初始化Vue

首先声明一个vue函数，我们将Vue在其原型上增加方法。

```
import { intiMixin } from './init'

function Vue(options) {
    this._init(options)
}

//扩展功能
intiMixin(Vue)
export default Vue;

```

在init文件中，创建 intimixin函数，接收Vue作为参数，并且新增init初始化方法

```
export function intiMixin(Vue) {
    Vue.prototype._init = function (options) { //初始化
        let vm = this
        vm.$options = options //挂载在实例上。
    }
}
```
在vue2中，$代表vue自身的属性，将传过来的参数，赋值到vue上

## 监听

这时候来监听options中的data,声明一个initState函数，如果是对象，循环对象中的key，用Object.defineProperty重写set与get方法

```
    Object.defineProperty(data, key, {
        get() {
            console.log('获取数据');
            return value
        },
        set(newValue) {
            console.log('写入数据了！');
            if (newValue !== value) {
                value = newValue
            }
        }
    })
```

如果是数组，则重写数组的7个更改方法！ 

```
//数组
 value.__proto__ = oldportotype

```

```
const func = ['push', 'shift', 'unshift', 'pop', 'srot', 'splice', 'reverse']
export const oldportotype = Array.prototype

func.forEach(key=>{
        oldportotype[key] = (...arg) => {
        switch (key) {
            case 'push':
                console.log('push');
                insert = arg
                break;
            case 'splice':
                insert = arg.slice(2)
                break;
            default:
                break;
        }
    }
})

```

将数组的隐式原型链覆盖，就监听到了数组的变化。

vue2中的$set方法，也是基于此。

set数组相当于splice数组，会触发重写的方法。而对象，则是向上直接添加响应式属性，再通知页面更新。