---
title: React优雅封装webscoket
date: 2023-09-16 15:10:48
tags:
    - webscoket
    - react
categories: 业务
---

在react中优雅使用webscoket,最好用到自定义hoc。
<!-- more -->

首先定义一个useSocket()方法，传入一些我们需要的配置文件。例如：

```
export type SocketOptions = {
  url: URL | string; // 连接的url地址
  type: 'situation' | 'single'; // 应用类型
  onOpen?: Function; // 自定义onopen回调函数
  onError?: Function; // 自定义onerror回调函数
  onClose?: Function; // 自定义onclose回调函数
  onMessage?: Function; // 自定义onmessage回调函数
  reconnect?: boolean; // 是否开启失败重连
  reconnectCount?: number; // 最大失败重连次数
  heartCheck?: boolean; // 是否开启心跳检测
  showMsg?: boolean; // 是否显示错误提示
};

```

新建webscoket，并且setState返回回来的数据。

```
function useSocket(options: SocketOptions) {
   let websocket: any = React.useRef<WebSocket>();
   const [socketData, setsocketData] = useState()

   const createWebSocket = () =>{} //连接函数
   ...

  return React.useMemo<any>(() => ({
    websocket,
    socketData,
    createWebSocket
  }), [websocket, socketData, createWebSocket]);
}
```
重写连接websocket，心跳回调函数

```
  options.reconnect = options?.reconnect || true; // 默认开启失败重连
  options.heartCheck = options?.heartCheck || true; // 默认开启心跳检测
  let websocket: any = React.useRef<WebSocket>();

  const [socketData, setsocketData] = useState()


  const lockReconnect = React.useRef<boolean>(false); // 锁住重连
  const connectNum = React.useRef<number>(0); // 已重连次数
  const timeoutObj: any = React.useRef(null); // 心跳检测定时器

  const reconnectFunc = React.useCallback(
    (callback: Function) => {
      if (lockReconnect.current) return;
      const reconnectCount = options.reconnectCount || config.wsReconnectNum;
      // if (!config.IsDev && connectNum.current < reconnectCount) {
      if (connectNum.current < reconnectCount) {
        setTimeout(() => {
          callback();
          connectNum.current = connectNum.current + 1;
          console.log(`ws失败重连第${connectNum.current}次`);
        }, 3000);
      }
    },
    [lockReconnect, connectNum, options]
  );
  const resetHeart = React.useCallback(() => {
    timeoutObj.current && clearInterval(timeoutObj.current);
    timeoutObj.current = null;
  }, [timeoutObj]);

  const startHeart = React.useCallback(() => {
    timeoutObj.current = setInterval(function () {
      websocket.current && websocket.current.send("HeartBeat");
    }, 6000);
  }, [timeoutObj, websocket]);

  const createWebSocket = React.useCallback(() => {
    if (!websocket.current) {
      websocket.current = new WebSocket(options.url);
      websocket.current.onopen = (res: any) => {
        console.log(options.url + "连接成功");
        connectNum.current = 0;
        options?.onOpen && options.onOpen(res);
        if (!config.IsDev && options.heartCheck) {
          resetHeart();
          startHeart();
        }
      };
      websocket.current.onerror = (err: any) => {
        options?.onError && options.onError(err);
        options?.reconnect && reconnectFunc(() => createWebSocket());
        if (options.showMsg) {
          generalNotification.error("websocket连接错误");
        }
      };
      websocket.current.onclose = (res: any) => {
        websocket.current && websocket.current.close();
        websocket.current = ''
        options?.onClose && options.onClose(res);
        if (options.showMsg) {
          generalNotification.error("websocket连接错误");
        }
      };
      websocket.current.onmessage = function (event: any) {
        setsocketData(event.data)
      };
    }
  },
    [
      websocket,
      options,
      connectNum,
      reconnectFunc,
      resetHeart,
      startHeart,
    ]);
```

这样，一个监听Webscoket数据来源的就封装好了。

使用：
```
  const socket = useSocket({
    type: 'type',
    url: 'url'
  })

  useEffect(() => {
    socket.createWebSocket()
    return () => {
    }
  }, [socket])

   //监听socketData
   useEffect(() => {
    if (socket.socketData) {
    }
    return () => {
    }
    // eslint-disable-next-line
  }, [socket.socketData])
```
这样，就封装好了一个Webscoket，需要修改配置的话，也可以直接在option里或者代码里面修改。