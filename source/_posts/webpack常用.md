---
title: webpack概念
date: 2021-03-28 19:15:29
tags:
    - webpack
categories: [学习,配置,打包]
---

## 插件

 html-webpack-plugin：编译HTML
 <!-- more -->

clean-webpack-plugin：清空打包内容

css-minimizer-webpack-plugin 压缩CSS

terser-webpack-plugin 压缩JS

 mini-css-extract-plugin 抽离CSS样式

 ## loader

 css-loader、style-loader、less-loader、postcss-loader 处理CSS的

 babel-loader 编译JS的

 file-loader、url-loader 处理图片的

 ESLint-loader 代码检测的


 ## 代理

 ```
  devServer: {
        proxy: {
            '/api': 'http://localhost:3000'
        }
    }
 ```

 ## 打包模式

 ```
  mode: "production", //打包模式，有三种：development开发模式、production生产模式、none不开启任何模式
 ```