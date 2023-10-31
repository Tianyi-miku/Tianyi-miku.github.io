---
title: solidity可升级合约
date: 2022-07-15 20:15:26
tags:
    - solidity
    - web3
    - Hardhat
categories: [学习,solidity]
---

可升级合约一般都伴随着风险存在，市面上大多数做法通常是通知用户部署新合约的地址，但是有部分依然使用可升级合约。
<!-- more -->
升级机制一般包括 合约迁移 数据分离迁移 代理模式 策略模式（小规模） 钻石模式（代理模式改进）

这里做一个代理合约。可升级合约一般有三个合约，旧合约，新合约，代理合约。

主调用合约的时候，走逻辑合约。

```

contract Upgrade {
    address public admin; //admin
    address public implementation; //代理合约地址
    string public words; //储存的数据

    constructor(address _implementation) {
        admin = msg.sender;
        implementation = _implementation;
    }

    fallback() external payable {
        (bool success, bytes memory data) = implementation.delegatecall(
            msg.data
        );
    }

    function upgrade(address newImplementation) external {
        require(msg.sender == admin);
        implementation = newImplementation;
    }
}
```

逻辑合约1

```
// 逻辑合约1
contract Logic1 {
    // 状态变量和proxy合约一致，防止插槽冲突
    address public implementation;
    address public admin;
    string public words; // 字符串，可以通过逻辑合约的函数改变

    // 改变proxy中状态变量，选择器： 0xc2985578
    function foo() public {
        words = "old";
    }
}
```

逻辑合约2

```
// 逻辑合约2
contract Logic2 {
    // 状态变量和proxy合约一致，防止插槽冲突
    address public implementation;
    address public admin;
    string public words; // 字符串，可以通过逻辑合约的函数改变

    // 改变proxy中状态变量，选择器：0xc2985578
    function foo() public {
        words = "new";
    }
}
```