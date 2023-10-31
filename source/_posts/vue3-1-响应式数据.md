---
title: vue3 响应式数据
date: 2023-05-12 12:05:13
tags:
    - vue3
    - 源码
categories: [学习,笔记]
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

vue2对数组重写方法，没有实现对索引和长度监听，所以在vue2中，key在做diff算法的时候就很重要了。

但是vue3中,数组也不用重写方法，而是访问的时候直接返回reactive（）数据，做的懒代理。


## ref

ref采用Object.defineProperty实现。基本类型转为对象来拦截！所以要.value来取值。

当然如果是对象，则将value转为reactive!


伪代码
```
function reactive(obj) {
  return new Proxy(obj, {
    get(target, key) {
      track(target, key)
      return target[key]
    },
    set(target, key, value) {
      target[key] = value
      trigger(target, key)
    }
  })
}

function ref(value) {
  const refObject = {
    get value() {
      track(refObject, 'value')
      return value
    },
    set value(newValue) {
      value = newValue
      trigger(refObject, 'value')
    }
  }
  return refObject
}

```