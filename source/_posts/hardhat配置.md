---
title: Hardhat配置
date: 2022-03-16 19:30:29
tags:
    - solidity
    - web3
    - Hardhat
categories: [学习,配置,框架]
---

首先打开hardhat的[官网](https://hardhat.org/)
<!-- more -->

初始化package.json
```
yarn init -y
```
添加hardhat
```
yarn add --dev hardhat
```
初始化一个hardhat框架
```
npx hardhat init
```
这时候会提示新建哪种项目，选择typescripte框架，等待下载完成

打开README.MD文件

```shell
npx hardhat help
npx hardhat test
REPORT_GAS=true npx hardhat test
npx hardhat node
npx hardhat run scripts/deploy.ts
```
这样一个hardhat基本框架就搭建好了。

## 基础配置

打开hardhat.config.ts,solidity一次性可配置多个编译版本。
```
const config: HardhatUserConfig = {
  solidity: {
    compilers: [
      { version: "0.8.9" },
      { version: "0.4.16" },
      { version: "0.4.24" },
    ],
  },
};

```
网络配置，这里示例配置了本地环境，sepolia与polygon链、PRIVATE_KEY就是私钥。
```
  defaultNetwork: "sepolia", //默认网络环境
  networks: {
    localhost: {
      url: 'http://127.0.0.1:8545/',
      chainId: 31337,
      accounts: [PRIVATE_KEY],
    },
    sepolia: {
      url: SEPOLIA_RPC_URL,
      accounts: [PRIVATE_KEY],
      chainId: 11155111,
      timeout: 80000,
    },
    polygon: {
      url: polygon_RPC_URL,
      accounts: [PRIVATE_KEY],
      chainId: 80001,
      timeout: 80000,
    }
  },
```
区块链浏览器配置,API key是在链上申请的key

```
  etherscan: {
    apiKey: {
      sepolia: SEPOLIA_APIKEY,
      polygonMumbai: polygon_ApiKey
    }
  },
```

gasReporter配置，gas显示费用。默认输出gas-report.txt文件。

```
  gasReporter: {
    enabled: true,
    currency: "USD",
    outputFile: "gas-report.txt",
    noColors: true,
    coinmarketcap: COINMARKETCAP_API_KEY,
  },
```

## 其他配置

因为网络原因，一般来说国内要配置翻墙代理
```
//翻墙代理-必须
const { ProxyAgent, setGlobalDispatcher } = require("undici");
const proxyAgent = new ProxyAgent('http://127.0.0.1:7890'); // change to yours
setGlobalDispatcher(proxyAgent);
```