---
title: vue2使用webwork链接websocket监听数据更新
date: 2019-04-13 19:40:18
tags:
    - vue2
    - websocket
categories: [业务]
---

## 第一步

先在store中声明好需要存储的数据，这里用vuex。
<!-- more -->
​
```
 state: {
    testName: 'hello',
   
  },
mutations: {
    setTestName(state, name) {
      state.testName = name
    }
}
```

创建一个worker文件，连接websocket。这里用单个的ws，多个可以自己修改。

```
let WS
let lockReconnect = false // 避免重复连接
const webWorkerSelf = self
const HeartBeat = {
  msgType: 'U',
  msgData: {
    ip: '127.0.0.1',
  },
  dataType: 'keepAlive',


}
//主线程
self.addEventListener(
  'message',
  function(e) {
    const data = e.data
    switch (data.cmd) {
      case 'sendOnline':
        heartCheck.sendOnline()
        break
      case 'config':
        Object.assign(HeartBeat.msgData)
        break
      case 'start':
        self.postMessage({
          type: 'webWorker',
          data: '启动webWorker'
        })
        createWebSocket()
        break
      case 'stop':
        WS.close()
        self.postMessage({
          type: 'webWorker',
          data: '停止webWorker'
        })
        // 服务器断开链接
        self.postMessage({
          type: 'keepalive',
          data: 'close' + new Date().toTimeString()
        })
        self.close() // 终止worker
        break
      default:
    }
  },
  false
)


const createWebSocket = function() {
  if (!WS) {
    WS = new WebSocket(url)
    WS.onerror = function(event) {
      reconnect(url)
    }
    WS.onclose = function(event) {
      reconnect(url)
    }
    WS.onopen = event => {
      // 服务器开启链接
      webWorkerSelf.postMessage({
        type: 'keepalive',
        data: 'start' + new Date().toTimeString()
      })
      heartCheck.reset().start() // 传递信息
    }
    WS.onmessage = e => {
      // 如果获取到消息，发送给主线程
      const data = JSON.parse(e.data)
      self.postMessage({
        type: 'webSocket',
        data
      })
      heartCheck.reset().start()
    }
  }
}
/***
 * 心跳检查
 * @type {{timeoutObj: null, start: heartCheck.start, reset: heartCheck.reset, timeout: number}}
 */
const heartCheck = {
  timeout: 30 * 1000, // 检查时间
  timeoutObj: null,
  serverTimeoutObj: null,
  reset: function() {
    clearTimeout(this.timeoutObj)
    clearTimeout(this.serverTimeoutObj)
    return this
  },
  // 发送在线信息
  sendOnline: function() {
    if (WS.readyState === 1) {
      WS.send(JSON.stringify(HeartBeat))
    }
  },
  close: function() {
    this.serverTimeoutObj = setTimeout(() => {
      // 如果超过一定时间还没重置，说明后端主动断开了
      // 服务器断开链接
      webWorkerSelf.postMessage({
        type: 'keepalive',
        data: 'close' + new Date().toTimeString()
      })
      WS.close() // 如果onclose会执行reconnect，我们执行ws.close()就行了.如果直接执行reconnect 会触发onclose导致重连两次
    }, this.timeout)
  },
  start: function() {
    this.timeoutObj = setTimeout(() => {
      // 这里发送一个心跳，返回一个心跳消息
      // onmessage拿到返回的心跳就说明连接正常
      this.sendOnline()
      this.close()
    }, this.timeout)
  }
}

/**
 * websocket重连
 */
function reconnect(url) {
  if (lockReconnect) return
  lockReconnect = true
  // 没连接上会一直重连，设置延迟避免请求过多
  setTimeout(function() {
    WS = null
    createWebSocket()
    lockReconnect = false
  }, 2000)
}

```

新建一个websocket文件将worker文件引入
```

<template>
  <div></div>
</template>

<script>
import Worker from './min.worker.js'



export default {
  name: 'socketView',
  data() {
    return {
      webSocket: null
    }
  },
  created() {
 
    this.initWebSocket()

  },

  methods: {
    initWebSocket() {
      const that = this
      // 用webworker子线程去连接webSocket
      this.webSocket = new Worker()
 
      this.webSocket.postMessage({ cmd: 'start' })
      this.webSocket.onmessage = function(event) {
        const { type, data } = event.data
        if (type === 'webSocket') {
             //这里调用store中的dispath或者commite储存数据
    
        } else if (type === 'keepalive') {
          //console.log('keepalive')
         
          if (data === 'close') {
            //
            //console.log('close')
          }
        }
      }
    },
    
  },
  beforeUnmount() {
    this.webSocket.postMessage({ cmd: 'stop' })
    setTimeout(() => {
      this.webSocket.terminate()
      this.webSocket = undefined
    })
  }
}
</script>

<style scoped></style>
```

再在页面中用computed监听数据，就能实现webscoket推送消息实时更新了。

​