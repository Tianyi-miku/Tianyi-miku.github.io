---
title: solidity学习
date: 2023-01-2 18:00:14
tags:
    - solidity
    - web3
categories: 学习
---

## solidity 基础

1. 存储类型 

基本类型包括布尔类型，整数型,地址，字节数组，枚举。
<!-- more -->

```
    bool public _bool = true;
    int public _int = -1;
    uint public _uint = 1;
    address public _address = 0x7A58c0Be72BE218B41C608b7Fe7C5bB630736C71;
       bytes1 public _byte = _byte32[0]; // bytes1: 0x4d
     // 将uint 0， 1， 2表示为Buy, Hold, Sell
    enum ActionSet { Buy, Hold, Sell }
```
引用类型包括结构体，与数组。
类型：storage memory 与Calldata
storage存储在链上，消耗gas多。

memory 与 calldata存储在临时内存，  



2. 函数类型

pure 既不能读取，也不能写入。
view 可以读取，不能写入
payable（可支付的）

函数描述

pubilic 公开的,内部外部都可以访问
private 私有的，继承的合约也不能访问
external 只能从外部访问（但是内部也可以通过this.function()调用）
internal 内部的，继承合约可以访问



