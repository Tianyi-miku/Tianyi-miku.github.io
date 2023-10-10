---
title: react常用hook
date: 2023-04-15 21:15:25
tags:
    - react
categories: [学习,笔记]
---
# react中useState

重点：函数组件，每次更新的时候都会重新执行函数！

首先声明一个usestate：
<!-- more -->

```
import React, { useState } from 'react'


const Renewal = () => {
    const [user, setUser] = useState({
        name: 'skymiku',
        age: 28
    })

    function changeUser() {
        setUser({
            ...user,
            age: user.age + 1
        })
        setUser({
            ...user,
            age: user.age + 1
        })
    }
    return (
        <div>
            <div>姓名： <span>{user.name}</span></div>
            <div>年龄: <span>{user.age}</span></div>
            <button onClick={() => changeUser()}>改变</button>
        </div>
    )
}
export default Renewal

```

每次渲染完之后，函数产生一个全新的私有上下文。

所以，在我们这样多次使用 age+1 的时候，点击一下age永远是29而不是30.

当然如果我们非要让他执行两次，可以将上一次的执行返回传递给下一次。

```
    setUser(val => {
            return {
                ...val,
                age: val.age + 1
            }
        })
        setUser(val => {
            return {
                ...val,
                age: val.age + 1
            }
        })
```

# useState方法大概思路

```
let val
// function usestate(defaultValue) {
//     if (typeof defaultValue === 'undefined') {
//         state = defaultValue
//     }
//     const setValue = (newValue) => {
//         val = newValue
//         //更新视图
//     }
//     return [val, setValue]
// }

// let [num, setnum] = usestate(0)
// setnum(100)  //val = 100
// //组件更新之后再次执行函数
// let [num1, setnum1] = usestate(0)
// // num1 这时候是100
```
这样第一次执行usestate 后，初始值将赋值。当SetValue更新视图之后，再次触发函数的时候，val将不会再次赋值,而是将上次执行的结果,到第二次执行里面,这就实现了页面更新!

# useEffect 使用

```
   useEffect(() => {
        console.log(user.age);
        return () => {
        }
    }, [])

```
当依赖项为空数组的时候,只是第一次渲染时候执行!

```
  useEffect(() => {
        console.log(user.age);
        return () => {
        }
    }, [user.age])

```

当有依赖项的时候,依赖项改变的时候,执行effect函数

在一个函数组件中,多个useEffect,将会呈链表调用.


# useLayoutEffect 与 useEffect 区别

useLayoutEffect 在虚拟Dom的时候就执行,还没有渲染真实DOM!

useEffect 在渲染真实DOM后,组件更新,useEffect 又会渲染真实Dom.


所以频繁更新渲染的时候,useEffect 可以看出一些闪烁!

例如:
```
import React from 'react'

function Myuseeffect() {
    const [num, setNum] = React.useState(0)

 
    // React.useEffect(() => {
    //     console.log('渲染');
    //     if (num === 10) {
    //         setNum(0)
    //     }
    // }, [num])

    
    React.useLayoutEffect(() => {
        console.log('渲染');
        if (num === 10) {
            setNum(0)
        }
    }, [num])

    return (
        <div style={{ backgroundColor: num === 0 ? 'red' : 'green' }}>
            <button onClick={() => setNum(10)}>改变</button>
        </div>
    )
}

export default Myuseeffect

```

useLayoutEffect 真实DOM 渲染一次! 所以并没有闪烁! 而且useLayoutEffect比useEffect 先执行.因为在虚拟DOM那就执行!


# Ref 使用

通过useRef创建一个Ref对象

```
import React, { useRef, useEffect, useState } from 'react'

function MyRef() {

    const first = useRef(null)
    const [num, setNum] = useState(0)
    useEffect(() => {
        console.log(first);
        return () => {
        }
    }, [])

    return (
        <div ref={first}>
            这是{num}
        </div>
    )
}

export default MyRef
```

通过这种方式可以拿到dom元素! 在16中 creatRef 也可以创建Ref对象,但是在其中每次更新的时候,都会重新创建一个新的Ref对象.

所以在函数组件中,还是是用useRef来获取!


# useRef 搭配 useImperativeHandle 与 React.forwardRef 转发

在父组件中,声明一个Ref对象, 将ref对象传给子组件 .用React.forwardRef包裹函数子组件,并且 useImperativeHandle 在子组件中 可以自定义返回内容

```
import React, { useRef, useEffect, useState, useImperativeHandle } from 'react'

const MyRef = () => {
    const father = useRef(null)
    useEffect(() => {
        console.log(father.current);
        return () => {
        }
    }, [])

    return (
        <div>
            这是
            <Childern ref={father}></Childern>
            {father.current.name}
        </div>
    )
}

export default MyRef


const Childern = React.forwardRef(function Childern(prop, ref) {
    const [first, setfirst] = useState(1)
    useImperativeHandle(ref, () => {
        return {
            age: first,
            name: 'Childern'
        }
    })
    return <>
        <div>
            <p>孩子</p>
            <span ref={ref}></span>
        </div>
    </>

}) 

```

这样,子组件用useImperativeHandle,返回的属性就能被父组件Ref获取到!


# usememo 计算缓存

usememo 是一个优化hook 与vue 中的computed 类似.

当依赖的数据改变的时候,才会去重新执行useme里面的函数.


```
import React, { useMemo, useState } from 'react'

const Myusememo = () => {

    const [num, setnum] = React.useState(0)
    const [x, setx] = useState(0)


    const data = useMemo(() => {
        console.log('执行usememo');
        return num + 1
    }, [num])


    return (
        <div>
            usememo
            <div>num:{data}</div>
            <div>x:{x}</div>
            <button onClick={() => setnum(num + 1)}>改变num</button>
            <button onClick={() => setx(x + 1)}>改变x</button>
        </div>
    )
}

export default Myusememo
```

例子中,执行setx的时候,usememo里面的函数不会再次执行,因为没有添加x的依赖!

# usecallback

usecallback 包裹的函数,将会缓存起来.下次组件更新的时候,将调用缓存的函数地址.

```
import React, { useCallback, useState } from 'react'

let prev
const Myusecallback = () => {

    const [x, setx] = useState(0)

    const handle = useCallback(() => {
        if (!prev) {
            prev = handle
        } else {
            console.log(prev === handle);
        }
    },
        [],
    )

    handle()

    return (
        <div>
            usecallback
            <button onClick={() => setx(x + 1)}>改变x</button>
        </div>
    )
}

export default Myusecallback

```

像是上面的例子,每次改变X之后,重新渲染组件调用handle.第一次将handle缓存之后,每次的重新执行函数,handle总是第一次缓存的地址!

通常这种用法: 父组件更新,传给子组件.子组件不需要重新渲染,这时候可以将父组件传给子组件的函数用 usecallback 包裹.子组件用usememo 来判断是否更改.

但是usecallback也不是所有函数都需要,每次创建的时候也需要耗费时间和内存,所以要根据实际项目来判断是否需要缓存函数.


# useContext 上下文对象

组件嵌套的时候,通过父组件一层层传递是很麻烦的,这时候可以用useContext来处理.

在共同要用的地方新建一个context文件

例如：
```
import React from 'react'
const Content = React.createContext()
export default Content
```
在父组件中，用provider提供上下文context，value就是要传递给下面子组件用的方法或者属性。

```
import React, { useState } from 'react'
import Child from './childern'
import Content from './contex'

const MyContext = () => {
    const [first, setfirst] = useState(0)
    return (
        <div>
            useContext
            <Content.Provider value={{
                setfirst,
                num: first
            }}>
                <Child></Child>
            </Content.Provider>

        </div>

    )
}
export default MyContext

```
在子组件中导入context并使用

```
import React, { useContext, useState } from 'react'
import Content from './contex'

const Child = () => {
    const { setfirst, num } = useContext(Content)
    return (
        <div>
            first:{num}
            <button onClick={() => setfirst(num + 1)}>子</button>
        </div>
    )
}

export default Child

```

这样，就实现了上下文传递.