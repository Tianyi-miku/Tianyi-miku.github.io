---
title: vue3 compositionApi一般使用
date: 2019-12-22 18:25:10
tags:
    - vue3
categories: [学习,笔记]
---

在vue2项目中，一般我们用这种方式给子组件传递参数。父组件：
<!-- more -->
```
<template>
  <div id="app">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'

export default {
  name: 'App',
  components: {
    HelloWorld
  },
}
</script>
```


子组件：
```
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
  </div>
</template>

<script>
import Vue from 'vue'
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
}
</script>
```

但是如果参数一多，或者复杂，来回传递的工作就增加了。

在vue3中。我们可以这样使用。

首先先生成一个vue3项目。


新建一个ts或者js文件，我们这里叫AppHook，当然随便什么名字都行，上面声明你说需要的响应式数据。

```
import {  reactive } from "vue";
type AppStats = {
  people: {
    name: string;
    sex: string;
    age: number;
  };
};

export const myAppState = reactive<AppStats>({
  people: {
    name: "王小二",
    sex: "男",
    age: 10,
  },
});
```


然后，我们就可以直接在子组件或者父组件中引用他们。

父组件：
```
<template>
  <HelloWorld></HelloWorld>
  {{ people.name }}
</template>

<script lang="ts" setup>
import { myAppState } from "./AppHook";
import HelloWorld from "./components/HelloWorld.vue";
const { people } = myAppState;
</script>
```


helloworld子组件
```
<template>
  <div class="hello">
    <button @click="changeName">改名称</button>

    <button @click="changeAge">改年纪</button>
  </div>
</template>
<script lang="ts" setup>
import { myAppState } from "@/AppHook";

const { people } = myAppState;
const changeName = () => {
  people.name = "张小花";
};
const changeAge = () => {
  people.age = 5;
};
</script>
```
在子组件点击修改名称，发现父组件的值直接改变了，这样就少了传值的功夫。

而且，可以在里面直接使用computed 与watch。

在Apphook里面添加
```
export const addAge = computed(() => {
  console.log(`${myAppState.people.name}年纪改变了`);
  return myAppState.people.age + 1;
});
```
 在父组件中
```
<template>
  <HelloWorld></HelloWorld>
  {{ people.name }}
  {{ addAge }}
</template>

<script lang="ts" setup>
import { myAppState, addAge } from "./AppHook";
import HelloWorld from "./components/HelloWorld.vue";
const { people } = myAppState;
</script>

<style>
```

运行发现 点击修改年纪发现computed确实运行了。

watch同理:
```
watch(myAppState.people, (newQuestion, oldQuestion) => {
  console.log(`watch改变了！`);
});
```
点击修改，发现确实触发了Watch

这样使用，在平时开发中少了很多监听prop中的值，或者是 父子传值，子孙传值之类麻烦的事情。

甚至你可以抛弃vuex之类全局储存的插件，直接使用全局的响应式数据。只是开发时候要注意，避免代码混乱。​