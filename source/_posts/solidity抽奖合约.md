---
title: solidity抽奖合约
date: 2022-07-16 19:21:20
tags:
    - solidity
    - web3
    - Hardhat
categories: [学习,solidity]
---
## 随机数

链上没法生成真正的随机数,真正的随机数要用到预言机
<!-- more -->
根据keccak256 伪随机数
``` 
         /*
         * 例上随机只依赖区块哈希，调用者地址，和区块时间，
         * 想提高随机性可以再增加一些属性比如nonce等，但是不能根本上解决安全问题
         */
    function getRound() external view returns (uint256) {
        bytes32 randomBytes = keccak256(
            abi.encodePacked(
                blockhash(block.number - 1),
                msg.sender,
                block.timestamp
            )
        );
        return uint256(randomBytes);
    }
```
chainlink 预言机

```

```