---
title: 手写简单的useEffect与useState
date: 2023-10-17 17:10:25
tags:
    - react
categories:  [学习]
---
导出调用app函数，调用时候刷新
```
const root = ReactDOM.createRoot(document.getElementById("root"));

export const forceRefresh = () => {
  console.log("Force fresh <App />");
  root.render(<App />);
};

forceRefresh();
```
<!-- more -->
简单的useState
```
import { forceRefresh } from "./index";

let saveState = [];
let index = 0;
export function useMyState(state) {
  const curIndex = index;
  index++;
  saveState[curIndex] = saveState[curIndex] || state;
  const rtnState = saveState[curIndex];
  const setState = (newState) => {
    saveState[curIndex] = newState;
    index = 0;
    forceRefresh();
  };
  return [rtnState, setState];
}
```

简单的useEffect
```
let index = 0;
let clearCallbacks = [];
let dependencyList = [];
export function useeffect(callback, deps) {
  let curIndex = index;
  index++;
  const lastDeps = dependencyList[curIndex];
  const changed =
    !lastDeps || !deps || deps.some((dep, i) => dep !== lastDeps[i]);
  if (changed) {
    dependencyList[curIndex] = deps;
    const clearCallback = clearCallbacks[curIndex];
    if (clearCallback) clearCallback();
    clearCallbacks[curIndex] = callback();
  }
  return;
}

```