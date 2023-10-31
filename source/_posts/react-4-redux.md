---
title: react redux
date: 2022-04-15 15:15:25
tags:
    - react
categories: 学习
---
# redux 使用


打开redux 官网，在项目中下载插件
```
npm install @reduxjs/toolkit react-redux
```
<!-- more -->

接着下载Redux
```
npm install redux
```

这里的redux与vuex很像，都是通过存放单一状态树，然后通过ation来更新。

## 在顶部引入存储

```
import store from './app/store'

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)

```
## 创建单个存储示例。
```
import { createSlice } from '@reduxjs/toolkit'

export const counterSlice = createSlice({
  name: 'counter',
  initialState: {
    value: 0
  },
  reducers: {
    increment: state => {
      // Redux Toolkit 允许我们在 reducers 写 "可变" 逻辑。它
      // 并不是真正的改变状态值，因为它使用了 Immer 库
      // 可以检测到“草稿状态“ 的变化并且基于这些变化生产全新的
      // 不可变的状态
      state.value += 1
    },
    decrement: state => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload
    }
  }
})
// 每个 case reducer 函数会生成对应的 Action creators
export const { increment, decrement, incrementByAmount } = counterSlice.actions

export default counterSlice.reducer
```

## 添加到Store
```
import { configureStore } from '@reduxjs/toolkit'
import counterReducer from '../features/counter/counterSlice'

export default configureStore({
  reducer: {
    counter: counterReducer
  }
})
```

## 在组件中使用：

```
  const count = useSelector(state => state.counter.value)
  const dispatch = useDispatch()
  dispatch(increment())
```

## 总结：

在应用顶层用provider注入Store，创建单一存储树，用对应的dispatch来管理更新状态。