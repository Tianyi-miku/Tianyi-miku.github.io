---
title: react渲染
date: 2023-04-17 19:15:25
tags:
    - react
categories: [学习,笔记]
---
# 第一步 createRoot()

首先初始化一个react项目
<!-- more -->

打开index.js 

首先看见ReactDOM.createRoot(document.getElementById('root'))

官方的描述是这样的

 * Same as `createRoot()`, but is used to hydrate a container whose HTML contents were rendered by ReactDOMServer.
 *
 * React will attempt to attach event listeners to the existing markup.
 *
 * **Example Usage**
 *
 * ```jsx
 * hydrateRoot(document.querySelector('#root'), <App />)
 * ```

 意思就是与createRoot()相同,创建根节点。


 # render 函数

 render函数是将虚拟dom转换为真实dom的一个函数。利用babeljs中的 babel-preset-react-app 将虚拟dom中的对象，转换为了真实dom。



 打开bable在线网站
![Alt text](/img/image.png)
 
 如图所示，插件将jsx编译为React.creatEelment(...)这种格式。
 ```    
    React.createElement(
    React.Fragment,
    null,
    React.createElement("h2", {
        className: "user"
    }, "skymiku"),
    React.createElement("div", {
        className: "title"
    },
        "react \u6E90\u7801\u5B66\u4E60\u4EE5\u53CA\u4F8B\u5B50!",
        React.createElement("div", null, "\u4F8B\u5982\uFF1A")
    )
    );
 ```

打印一下，可以看出虚拟Dom对象长这样的。

![Alt text](/img/image1.png)
```
    vitrualDom = {
        $$typeof:Symbol(react.element),
        key：null,
        prop: {
            children //子节点
        },
        ref：null,
        type: 标签名称或者组件，
    }

```

模拟一个创建一个虚拟dom,在Src下创建一个createElement文件夹放入jsxHandle.js。代码如下

```
export default function createElement(ele, prop, ...children) {
    let vitrualDom = {
        $$typeof: Symbol('react.element'),
        key: null,
        prop: {},
        ref: null,
        type: null
    }

    vitrualDom.type = ele;

    if (prop) {
        vitrualDom.prop = { ...prop }
    }

    vitrualDom.prop.children = children.length === 1 ? children[0] : children
    return vitrualDom;
}
```

将 React.createElement 替换成上面的 createElement，打印一下，结构一致。

这样我们的模拟创建虚拟dom就创建好了。

### 虚拟dom渲染成真实dmo render

创建一个render函数。将虚拟dom 和传入。并且循环将其属性处理。注意，prop中的children递归处理时候，要判断是否为字符串和number类型。

```
//render 函数

//render 函数
export function render(vitrualDom, container) {
    let { type, prop, props } = vitrualDom
    //如果是标签名
    if (typeof type === 'string') {
        const ele = document.createElement(type)
        each(prop || props, (key, value) => {
            //判断是否是className,与style
            if (key === 'className') {
                ele.className = value
                return
            }
            if (key === 'style') {
                each(value, (key, value) => {
                    ele.style[key] = value
                })
                return
            }
            //子节点分两种，唯一子节点直接添加，多个子节点递归
            if (key === 'children') {
                let children = value;
                //判断是否是文本节点
                if (isNumberOrstring(children)) {
                    ele.appendChild(document.createTextNode(children))
                    return
                } else {
                    children.forEach(item => {
                        //判断子节点的type是不是文本节点
                        if (isNumberOrstring(item)) {
                            //如果是子节点直接添加
                            ele.appendChild(document.createTextNode(item))
                            return
                        } else {
                            render(item, ele)
                        }
                    })
                }
                return
            }
            ele.setAttribute(key, value)
        })
        container.appendChild(ele)
    } else {
        if (prop.children?.length > 1) {
            prop.children.forEach(value => {
                render(value, container)
            })
        }
    }
}

function isNumberOrstring(params) {
    if (typeof params === 'string' || typeof params === 'number') {
        return true
    }
    return false
}


function each(obj, callback) {
    if (obj === null || typeof obj !== "object") {
        throw new TypeError('obj is not object')
    }
    if (typeof callback !== "function") {
        throw new TypeError('callback is not function')
    }
    const keys = Reflect.ownKeys(obj)
    keys.forEach(key => {
        const value = obj[key];
        callback(key, value)
    })
}
```


将index中的root.render换成我们的render函数

![Alt text](/img/image2.png)

这样，就大概熟悉了React中，虚拟dom的创建与转换真实Dom的流程了。

