---
title: vue2依赖收集
date: 2022-04-2 18:05:13
tags:
    - vue2
    - 源码
categories: 学习
---
## 对象依赖收集
在响应式数据收集的时候，也就是Object.defineProperty get 的时候，会加一个被观察者dep, 全局有一个Dep类。

在渲染的时候watcher 里面会调用render方法。在Render取值之前，会将Dep静态属性target的值指向wathcer。当然如果页面上没有这个值，就不会触发render取值，也就不会触发依赖收集。

在render中实现取值的时候，触发get。get中判断全局静态属性target。

如果有值，让dep记住watcher.然后将watcher保存在dep中的subs数组里面。

最后删除Dep 中全局tagert。


## 更新

当set 值发生变化的时候，调用dep中的notify 去通知更新组件！

notify中，循环Watcher去更新每一个组件！

## 类似例子：

响应式数据创建时候添加dep
```
function proxy(obj, key, value) {
    let dep = new Dep()
    Object.defineProperty(obj, key, {
        get() {
            if (Dep.target) {  //这里查看到属性的Watcher，Dep target是一开始渲染的时候，声明的全局属性，指向对应的watcher.
                dep.depend()
            }
            return value
        },
        set(newValue) {
            value = newValue
            dep.notify() //通知更新
        }
    })
}
```

页面渲染的时候，调用watcher中的render取值

```
let id = 0
class watcher {
    constructor() {
        id = id++
        this.deps = []
        this.depId = new Set()
        this.get()         //渲染的时候调用Render 会调用get 取值,在defineProperty中可以看到dep 中的Watcher
    }

    get() {
        Dep.target = this  //创建渲染watcher时候，将Dep中的target放入watcher，
        vm._update(vm.render()) //vm是虚拟dom
        Dep.target = null
    }
    addDep(dep) {
        id = id
        this.deps.push(dep)
        dep.addSub(this)
    }

    _update() {
        this.get() //重新渲染
    }
}

```

dep修改数据的时候，通知更新
```
let depid = 0
class Dep {
    constructor() {
        this.depid = depid++
        this.subs = []
    }

    depend() {
        Dep.target.addDep(this)
    } //存入Watcher

    addSub(watcher) {
        this.subs.push(watcher)
    }

    notify() {
        this.subs.forEach(watcher => watcher._update())
    }
}
```

## 异步渲染

异步渲染是要解决多次赋值，多次渲染页面，要实现只渲染一次!

watcher更新之前，先将watcher存在一个队列中，判断是否已经存在相同的watcher,不存在再加入队列。

```
let queue = []
let has = {}
let padding = false

function queueWatcher(watcher) {
    const id = watcher.id
    if (!has[id]) {
        queue.push(watcher);
        has[id] = true;

        if (!padding) {
            setTimeout(() => {         //vue中用nexttick
                flushSchedulerQueue()
                padding = true
            }, 0);
        }
    }
}

```
## nexttick

nexttick 有内部调用和外部直接调用，这里也是用队列执行，谁在前面先执行谁的。

```
let callback = []
let waiting = false

function flushCallbacks() {
    let cbs = callback.slice(0)
    waiting = false
    callback = []
    cbs.forEach(cb => cb())
}

function nextTick(cb) {
    callback.push(cb);
    if (!waiting) {
        timerFc()  //优雅降级 promise => MutationObserve => setimmediate =>settimeout
        waiting = true
    }
}
```

nexttick异步为优雅降级，先判断浏览器是否支持promise，再到H5的mutationObserve 在到ie的setimmediate 最后是开线程settimeout

```

let timerFc;
if (Promise) {
    timerFc = () => {
        Promise.resolve().then(flushCallbacks)
    }
} else if (MutationObserver) {
    let observer = new MutationObserver()
    let textNode = document.createTextNode(1)
    observer.observe(textNode, {
        characterData: true
    })
    timerFc = () => {
        textNode.textContent = 2
    }
} else if (setImmediate) {
    setImmediate(flushCallbacks)
} else {
    timerFc = setTimeout(() => {
        flushCallbacks
    }, 0);
}

```



## 总结

1.首先响应式数据中，每一个get方法中 都有一个通过Dep构造函数，生成dep函数。

2.在浏览器解析完ast树，用渲染watcher去触发render函数，将watcher存放在全局Dep构造函数中的target中，维护成一个栈。

3.调用render函数，会触发响应式数据中的get方法。

4.get 方法中将全局中Dep target取出，就是对应的Wather。然后调用wacher中存放dep的函数，这样就实现了watcher与数据的绑定！！

5.为了优化渲染，页面组件多次赋值，执行一次。将渲染render之前触发的watcher放入队列中，去重。这样就达到了只渲染一次。

6.这里为了实现异步渲染，暴露一个nexttick方法实现更新。原理是通过优雅降级的方式，实现异步。promise => MutationObserver => setimmediate => settimeout

