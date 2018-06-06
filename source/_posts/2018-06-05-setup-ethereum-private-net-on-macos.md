---
title: 在 macOS 中搭建 Ethereum 私有链
date: 2018-06-05 18:41:12
tags: [BlockChain, Ethereum, macOS]
categories:
---

## 1. 安装 Go 语言开发环境
参见 Golang 官方安装向导: https://golang.org/doc/install

<br/>

## 2. 安装 Geth 客户端

```bash
$ brew tap ethereum/ethereum # 添加仓库
$ brew install ethereum  # 安装 go-ethereum
```

> 通过 `geth -h` 查看是否安装成功

<!-- more -->

<br/>

## 3. 创建配置文件

在创建私有链前, 需要新建一个文件夹来存放私有链中相应的数据文件.  
假设已经创建了该文件夹, 切换到该目录下, 在里面创建一个 `genesis.json` 文件

```bash
$ touch genesis.json  # 创建配置文件
```

把以下内容写到 `genesis.json` 文件中:

```json
{
  "config": {
        "chainId": 15,
        "homesteadBlock": 0,
        "eip155Block": 0,
        "eip158Block": 0
    },
    "coinbase" : "0x0000000000000000000000000000000000000000",
    "difficulty" : "0x40000",
    "extraData" : "",
    "gasLimit" : "0xffffffff",
    "nonce" : "0x0000000000000042",
    "mixhash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "parentHash" : "0x0000000000000000000000000000000000000000000000000000000000000000",
    "timestamp" : "0x00",
    "alloc": { }
}
```

- coinbase: 矿工的账号，可随便填.
- difficulty: 设置当前区块的难度，如果难度过大，cpu挖矿就很难，这里设置一个较小的难度值.
- extraData: 附加信息，随便填，可以填我们的个性信息.
- gasLimit: 该值设置对 gas 的消耗总量限制，用来限制区块能包含的交易信息总和，因为我们是私有链，所以可以填个最大值.
- nonce: nonce 就是一个64位随机数，用于挖矿(注意它和 mixhash 的设置需要满足以太坊的Yellow paper, 4.3.4. Block Header Validity, (44)章节所描述的条件)
- mixhash: 与 nonce 配合用于挖矿，由上一个区块的一部分生成的hash.(注意它和 nonce 的设置需要满足以太坊的 Yellow paper, 4.3.4. Block Header Validity, (44)章节所描述的条件)
- parentHash: 上一个区块的hash值，因为是创世块，所以这个值是0.
- timestamp: 设置创世块的时间戳.
- alloc: 用来预置账号以及账号的以太币数量，因为私有链挖矿比较容易，所以我们不需要预置有币的账号，需要的时候自己创建即可以.

<br/>

## 4. 初始化私有链

```bash
$ geth init ./genesis.json --datadir "./chain"
```

- datadir: 指定数据存放的文件夹

<br/>

## 5. 启动私有链
```bash
$ geth --datadir "./chain" --networkid 15 --identity "TestNode" --rpc \
$ --rpcport "8545" --port "30303" --nodiscover console 2>console.log
```

- datadir: 指定当前区块链网络数据存放的位置
- networkid: 设置当前区块链的网络ID，用于区分不同的网络，是一个数字(1表示 Ethereum 主链, 默认为1)
- identity: 区块链的标示，随便填写，用于标示目前网络的名字
- rpc: 启动rpc通信，可以进行智能合约的部署和调试
- rpcport: rpc端口号. 默认为8545
- port: P2P网络监听端口. 默认为30303
- nodiscover: 禁止被发现. 添加这个参数可以确保你的节点不会被非手动添加你的人发现. 否则，你的节点可能因为你与其它的链有相同的创世文件和网络ID而被陌生人的区块链无意添加.
- console: 启动命令行模式，可以在Geth中执行命令

<br/>

## 6. 使用 Ethereum Wallet 连接本地私有链
先到 Ethereum 的官网下载 Wallet 应用: https://www.ethereum.org

安装 Ethereum Wallet 应用后, 若直接运行, 它将会尝试连接 Ethereum 的主网络.  
若我们没创建私有链, 或者没有启动本地私有链的话, 运行 Ethereum Wallet 应该是没问题的. 如果我们启动了本地私有链并且没有更改默认端口 30303, 打开 Ethereum Wallet 会报无法连接节点的错误:
> Fatal: Error starting protocol stack: listen tcp :30303: bind: address already in use

这是因为我们刚启动的私有链占用了 30303 端口, Ethereum 启动也需要使用 30303 端口.

所以这里我们使用命令行的方式启动 Ethereum Wallet:

```bash
$ cd /Applications/Ethereum\ Wallet.app/Contents/MacOS
$ ./Ethereum\ Wallet --rpc "私有链的ipc地址"
```







