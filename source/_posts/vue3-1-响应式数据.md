---
title: vue3 响应式数据
date: 2022-05-12 12:05:13
tags:
    - vue3
    - 源码
categories: 学习
---

## vue3 响应式数据

在vue2中，主要是使用Object.defineProperty来重写对象中的get 与 set 方法。数组则是采用重写数组的方法来实现数据拦截。

而在vue3中，主要是使用Proxy。

<!-- more -->

```
function reactive(target) {
    return new Proxy(target,{
        get(target,key){

        },
        set(target,key,value){

        }
    })
}
const obj = {name:'skymiku'}
const proxy = reactive(obj)
```

当然在vue中除了reactive，还有shallowReactive 浅层次代理，readonly，shallowReadonly。

所以在vue3中就用了柯里化的方式来构建，类似:
```
const reactiveMap = new WeakMap() //弱引用
const readonlyMap = new WeakMap()

function creatReactiveObject(target, isReadonly, baseHandlers) {
    if (!isObject(target)) {
        return target
    }
    const proxyMap = isReadonly ? readonlyMap : reactiveMap

    const existProxy = proxyMap.get(target) //查看代理过没有
    if (existProxy) {
        return existProxy
    }
    //代理
    const proxy = new Proxy(target, baseHandlers)
    proxyMap.set(target, proxy)

    return proxy
}

function reactive(target) {
    return creatReactiveObject(target, false, reactiveHandlers)
}

function shallowReactive(target) {
    return creatReactiveObject(target, false, shallowReactiveHandlers)
}

function readonly(target) {
    return creatReactiveObject(target, true, readonlyHandlers)
}

function shallowReadonly(target) {
    return creatReactiveObject(target, false, shallowReadonlyHandlers)
}

```

当然核心还是proxy，比起vue2中的来说，它只有在取值的时候，才会触发get方法，不用一开始就循环属性拦截。并且，不存在的属性的时候，他也能监听！


vue3中数组也不用重写方法，而是发现他是数组之后，返回reactive（）数据，做的懒代理。