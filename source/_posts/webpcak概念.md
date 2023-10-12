---
title: webpack概念
date: 2021-03-27 18:55:19
tags:
    - webpack
categories: [打包,配置]
---

## 入口

打包的时候，找寻的入口。
<!-- more -->

```
module.exports = {
  entry: './path/to/my/entry/file.js',
};
```

## 出口

打包完成后，输出的文件目录，可以自定义所在文件夹。

```
module.exports = {
  entry: './path/to/my/entry/file.js',
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'my-first-webpack.bundle.js',
  },
};
```
## loader

webpack 只能解析js与json，其他的css之类的要靠loader去转换。

```
const path = require('path');

module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js',
  },
  module: {
    rules: [{ test: /\.txt$/, use: 'raw-loader' }],
  },
};
```

## plugin插件

可以添加一些任务。比如打包优化，注入环境变量。

```
const path = require('path');

module.exports = {
  output: {
    filename: 'my-first-webpack.bundle.js',
  },
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' },
    ],
  },
};
```

## 模式

配置多种环境，比如开发环境，生产环境。

```
module.exports = {
  mode: 'production',
};
```
