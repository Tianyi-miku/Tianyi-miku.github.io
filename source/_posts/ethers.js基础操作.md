---
title: ethers.js基础操作
date: 2022-12-21 21:43:10
tags:
    - web3
categories: [学习,ethers.js]
---

ethers基础操作
<!-- more -->
Provider链接网络
```
// 利用ethers默认的Provider连接以太坊网络
// const provider = new ethers.getDefaultProvider();
const ALCHEMY_MAINNET_URL = 'https://eth-mainnet.g.alchemy.com/v2/oKmOQKbneVkxgHZfibs-iFhIlIAl6HDN';
const provider = new ethers.JsonRpcProvider(ALCHEMY_MAINNET_URL)
```
获取余额、网络等基础信息
```
 const balance = await provider.getBalance(`user`);
 const network = await provider.getNetwork();
 ....
```
发送币
```
    const tx = {
        to: address1,
        value: ethers.parseEther("0.001")
    }
    const address2 = await wallet2.getAddress() 
    const receipt = await wallet2.sendTransaction(tx)
    await receipt.wait() // 等待链上确认交易
    console.log(receipt) // 打印交易详情
```
调用合约
```
// WETH的ABI
const abiWETH = [
    "function balanceOf(address) public view returns(uint)",
    "function deposit() public payable",
    "function transfer(address, uint) public returns (bool)",
    "function withdraw(uint) public",
];
// 声明可写合约
const contractWETH = new ethers.Contract(addressWETH, abiWETH, wallet)
const tx = await contractWETH.deposit({value: ethers.parseEther("0.001")})
// 等待交易上链
await tx.wait()
console.log(`交易详情：`)
```
监听事件
```
   contractUSDT.once('Transfer', (from, to, value)=>{
      // 打印结果
      console.log(
        `${from} -> ${to} ${ethers.formatUnits(ethers.getBigInt(value),6)}`
      )
    })

```
事件过滤
```
    console.log("\n1. 读取币安热钱包USDT余额")
    const balanceUSDT = await contractUSDT.balanceOf(accountBinance)
    console.log(`USDT余额: ${ethers.formatUnits(balanceUSDT,6)}\n`)

    // 2. 创建过滤器，监听转移USDT进交易所
    console.log("\n2. 创建过滤器，监听USDT转进交易所")
    let filterBinanceIn = contractUSDT.filters.Transfer(null, accountBinance);
    console.log("过滤器详情：")
    console.log(filterBinanceIn);
    contractUSDT.on(filterBinanceIn, (res) => {
      console.log('---------监听USDT进入交易所--------');
      console.log(
        `${res.args[0]} -> ${res.args[1]} ${ethers.formatUnits(res.args[2],6)}`
      )
    })

```