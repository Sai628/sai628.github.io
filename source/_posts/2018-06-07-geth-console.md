---
title: go-ethereum 的常用 console 命令
date: 2018-06-07 13:04:48
tags: [BlockChain, Ethereum, geth]
categories:
---

当我们搭建了一条以太坊的私有链后, 使用 `geth console` 命令可启动节点, 并进入一个交互式的控制台.

```bash
$ geth --datadir "./chain" --networkid 15 console
```

运行上面命令后, 将会得到像以下的输出:

<!-- more -->

```bash
INFO [06-07|13:29:04] Maximum peer count                       ETH=25 LES=0 total=25
INFO [06-07|13:29:04] Starting peer-to-peer node               instance=Geth/v1.8.10-stable/darwin-amd64/go1.10.2
INFO [06-07|13:29:04] Allocated cache and file handles         database=/Users/Sai/opt/Ethereum/chain/geth/chaindata cache=768 handles=1024
... #中间省略 
...
WARN [06-07|13:29:04] Blockchain not empty, fast sync disabled
INFO [06-07|13:29:04] Starting P2P networking
INFO [06-07|13:29:04] UDP listener up                          self=enode://0f28c8850e733dabbcc4845191c3013c5c73c7d338cc62fc79318111765fd49db48724b0874b0ab32cd259f32feaf6e794a7a036d99749766c867ada0589ea9f@192.168.1.18:30303
INFO [06-07|13:29:04] RLPx listener up                         self=enode://0f28c8850e733dabbcc4845191c3013c5c73c7d338cc62fc79318111765fd49db48724b0874b0ab32cd259f32feaf6e794a7a036d99749766c867ada0589ea9f@192.168.1.18:30303
INFO [06-07|13:29:04] IPC endpoint opened                      url=/Users/Sai/opt/Ethereum/chain/geth.ipc
Welcome to the Geth JavaScript console!

instance: Geth/v1.8.10-stable/darwin-amd64/go1.10.2
INFO [06-07|13:29:04] Mapped network port                      proto=udp extport=30303 intport=30303 interface=NAT-PMP(192.168.199.1)
INFO [06-07|13:29:04] Etherbase automatically configured       address=0xD2b80AE509A46Af16B2dD7480C6b00e073133ad1
coinbase: 0xd2b80ae509a46af16b2dd7480c6b00e073133ad1
at block: 3735 (Wed, 06 Jun 2018 17:52:40 CST)
 datadir: /Users/Sai/opt/Ethereum/chain
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

> INFO [06-07|13:29:04] Mapped network port                      proto=tcp extport=30303 intport=30303 interface=NAT-PMP(192.168.199.1)

>
```

这是一个交互式的 Javascript 执行环境, 可以直接在 `>` 提示符后输入 Javascript 代码, 它会将执行后的结果在当前窗口中输出.  
不过很可能一些 `console` 的日志或辅助信息也会在当前窗口中输出, 影响 Javascript 代码结果的查看. 所以一个更好的启动 `geth console` 方式, 是使用以下的命令, 将 `console` 的输出重定向到一个文件中:

```bash
$ geth --datadir "./chain" --networkid 15 console 2> console.log
```

运行上面的命令后, 我们将进入一个更清爽的交互式环境:

```bash
Welcome to the Geth JavaScript console!

instance: Geth/v1.8.10-stable/darwin-amd64/go1.10.2
coinbase: 0xd2b80ae509a46af16b2dd7480c6b00e073133ad1
at block: 3735 (Wed, 06 Jun 2018 17:52:40 CST)
 datadir: /Users/Sai/opt/Ethereum/chain
 modules: admin:1.0 debug:1.0 eth:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

>
```

然后我们可以另起一个终端窗口, 使用命令 `tail -f console.log` 来查看 `console` 的日志或辅助信息输出.


在这个交互式的 Javascript 执行环境中, 内置了一些用来操作 Ethereum 节点网络的 Javascript 对象. 它们提供的功能很丰富, 比如: 查看区块与交易信息、创建账户、挖矿、发送交易、部署智能合约、管理节点、查看内存池状态等等. 在进入 Javascript 的执行环境后, 我们可以直接使用这些对象. 


__内置对象列表:__

<!-- 重写表格宽度的样式 -->
<style>
	table th:nth-of-type(1) {
		width: 100px;
	}
</style>

|对象|用处|
|:--|:---|
|personal|包含与账户管理相关的方法|
|admin|包含与节点节点相关的方法|
|miner|包含启动或停止挖矿的方法|
|eth|包含与操作区块链相关的方法|
|net|包含一些查看P2P网络状态的方法|
|txpool|包含一些查看交易内存池的方法|
|web3|包含了以上的对象, 还包含一些有关单位换算的工具类方法|

下面将介绍这些内置对象常用方法的使用. 前面带 `>` 字符的表示输入的 Javascript 命令代码, 其它的表示输出.

<br/>

## 1. 账户管理

### 1.1 查看账户

先查看一下当前节点的账户信息:
```bash
> personal.listAccounts
[]
```

或者使用以下命令查看:
```bash
> eth.accounts
[]
```

### 1.2 创建账户

此时节点网络中没有任何的账户, 我们可以使用 `personal` 对象来创建账户:
```bash
> personal.newAccount()
Passphrase:
Repeat passphrase:
"0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593"
```

也可以在创建账户时, 指定密码:
```bash
> personal.newAccount("123456")
"0xe74fc1abfbac8f598a979b11f23177f69f4de6de"
```

我们再次查看账户:
```bash
> personal.listAccounts
["0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593", "0xe74fc1abfbac8f598a979b11f23177f69f4de6de"]
```

查看指定账户:
```bash
> personal.listAccounts[0]
"0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593"
```

### 1.3 解锁账户

```bash
> personal.unlockAccount(eth.accounts[0])
Unlock account 0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593
Passphrase:
true
```

> __当进行发送交易时, 需要先对转出的账户进行解锁操作.__

可以指定过期时间, 单位为秒(以下为设置5分钟后解锁过期):
```bash
> personal.unlockAccount(eth.accounts[1], "123456", 60*5)
true
```

假如我们想在指定过期时间时, 依然在密码提示符后输入密码, 而不是直接在代码命令中输入密码明文, 可以在密码参数一项传入 `null`:
```bash
> personal.unlockAccount(eth.accounts[1], null, 60*5)
Unlock account 0xe74fc1abfbac8f598a979b11f23177f69f4de6de
Passphrase:
true
```

### 1.4 查看钱包的所有账户信息
```bash
> personal.listWallets
[{
    accounts: [{
        address: "0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593",
        url: "keystore:///Users/Sai/opt/Ethereum/chain/keystore/UTC--2018-06-07T06-24-49.837760495Z--4378f11e3d4a1e26381cfd285f3237d4b7fc5593"
    }],
    status: "Unlocked",
    url: "keystore:///Users/Sai/opt/Ethereum/chain/keystore/UTC--2018-06-07T06-24-49.837760495Z--4378f11e3d4a1e26381cfd285f3237d4b7fc5593"
}, {
    accounts: [{
        address: "0xe74fc1abfbac8f598a979b11f23177f69f4de6de",
        url: "keystore:///Users/Sai/opt/Ethereum/chain/keystore/UTC--2018-06-07T06-27-20.273464191Z--e74fc1abfbac8f598a979b11f23177f69f4de6de"
    }],
    status: "Locked",
    url: "keystore:///Users/Sai/opt/Ethereum/chain/keystore/UTC--2018-06-07T06-27-20.273464191Z--e74fc1abfbac8f598a979b11f23177f69f4de6de"
}]
```

<br/>

## 2. admin 管理

### 2.1 查看数据目录
```bash
> admin.datadir
"/Users/Sai/opt/Ethereum/chain"
```

### 2.2 显示当前节点信息
```bash
> admin.nodeInfo
{
  enode: "enode://a14d711a9cd37c00c10807edda1a0ad5f7076125132319cbc8ce1a37cfadb4db22028be6b8103bea208ceb114e4ed81fbd5f3fa927561a90b9f52b725ef4fa0e@192.168.1.18:30303",
  id: "a14d711a9cd37c00c10807edda1a0ad5f7076125132319cbc8ce1a37cfadb4db22028be6b8103bea208ceb114e4ed81fbd5f3fa927561a90b9f52b725ef4fa0e",
  ip: "192.168.1.18",
  listenAddr: "[::]:30303",
  name: "Geth/v1.8.10-stable/darwin-amd64/go1.10.2",
  ports: {
    discovery: 30303,
    listener: 30303
  },
  protocols: {
    eth: {
      config: {
        chainId: 15,
        eip150Hash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        eip155Block: 0,
        eip158Block: 0,
        homesteadBlock: 0
      },
      difficulty: 1024,
      genesis: "0x2720038ef46044a7a895296b85745294340ecfcedc32f8c9e9802129aeb62890",
      head: "0x2720038ef46044a7a895296b85745294340ecfcedc32f8c9e9802129aeb62890",
      network: 15
    }
  }
}
```

### 2.3 查看当前节点地址
```bash
> admin.nodeInfo.enode
"enode://a14d711a9cd37c00c10807edda1a0ad5f7076125132319cbc8ce1a37cfadb4db22028be6b8103bea208ceb114e4ed81fbd5f3fa927561a90b9f52b725ef4fa0e@192.168.1.18:30303"
```

> 其中的 192.168.1.18 表示 ipv4 地址, 也可能为 "[::]", 表示 ipv6 地址.

### 2.4 查看 networkid
```bash
> admin.nodeInfo.protocols.eth.network
15
```

### 2.5 添加其它节点

可以通过 `admin.addPeer("节点地址")` 方法连接到其它节点. 两个节点要想联通, 必须保证网络是相通的, 并且要指定相同的 networkid. 而且启动节点时, 注意去掉 --nodiscover 参数.

先确保网络可用:
```bash
> net.listening
true
```

添加其它节点:
```bash
> admin.addPeer("enode://87c10977acb3f48718e3a980a27f39e9c7a0bf00143f2b9143a22fa96e33e6b519d7e8407a32f5a61f9c6eb139d6bd46ad7f7288f169d0d0689e881d76e997c1@192.168.1.18:30301")
```

> 其中的节点地址, 是使用以下命令另外启动的一个节点:
> `geth --datadir "./chain2" --networkid 15 --port 30301 console 2> console2.log`

### 2.6 查看其它Peer节点信息
```bash
> admin.peers
[{
    caps: ["eth/63"],
    id: "87c10977acb3f48718e3a980a27f39e9c7a0bf00143f2b9143a22fa96e33e6b519d7e8407a32f5a61f9c6eb139d6bd46ad7f7288f169d0d0689e881d76e997c1",
    name: "Geth/v1.8.10-stable/darwin-amd64/go1.10.2",
    network: {
      inbound: false,
      localAddress: "192.168.199.141:62161",
      remoteAddress: "192.168.1.18:30301",
      static: true,
      trusted: false
    },
    protocols: {
      eth: {
        difficulty: 1024,
        head: "0x2720038ef46044a7a895296b85745294340ecfcedc32f8c9e9802129aeb62890",
        version: 63
      }
    }
}]
```

列表其它节点的 IP 地址:
```bash
> admin.peers.forEach(function(p) {console.log(p.network.remoteAddress)})
192.168.1.18:30301
undefined
```

<br/>

## 3. miner 挖矿管理

### 3.1 开始挖矿
```bash
> miner.start(2)
null
```

其中的参数表示挖矿时使用的线程数, 会根据计算机的硬件情况进行设置.  
第一次启动挖矿时, 会先生成挖矿所需的 DAG 文件, 这个过程可能会比较慢, 等进度达到 100% 后, 就会开始挖矿.

### 3.2 停止挖矿
```bash
> miner.stop()
true
```

### 3.3 设置矿工账户

当挖到一个区块得到以太币奖励时, 所得的奖励会进入到矿工的账户中.  
这个账户叫做 `coinbase`, 默认下矿工的账户为系统的第一个账户.  
我们也可以设置其它的账户为 `coinbase` 账户 :

```bash
> miner.setEtherbase(eth.accounts[1])
true
```

<br/>

## 4. eth 管理

### 4.1 查看矿工账户
```bash
> eth.coinbase
"0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593"
```

> 默认下 coinbase 为系统的第一个账户.

### 4.2 查看账号列表
```bash
> eth.accounts
["0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593", "0xe74fc1abfbac8f598a979b11f23177f69f4de6de"]
```

查看指定的账户:
```bash
> eth.accounts[0]
"0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593"
```

### 4.3 查看账户余额
```bash
> eth.getBalance(eth.accounts[0])
890000000000000000000
```

`getBalance` 方法返回值的单位是 wei, wei是以太币的最小单位.  
其中: 1以太币=10^18个wei.  
可以使用 `web3.fromWei()` 方法将返回值换算成以太币:

```bash
> u0 = eth.accounts[0]
"0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593"
> web3.fromWei(eth.getBalance(u0), 'ether')
890
```

### 4.4 一次查看所有的账户余额
```bash
> function checkAllBalances() {
...   web3.eth.getAccounts(function(err, accounts) {
.........    accounts.forEach(function(id) {
...............     web3.eth.getBalance(id, function(err, balance) {
.....................      console.log("" + id + ":\tbalance: " + web3.fromWei(balance, "ether") + " ether");
.....................    });
...............   });
.........  });
... };
undefined
> checkAllBalances()
0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593:	balance: 890 ether
0xe74fc1abfbac8f598a979b11f23177f69f4de6de:	balance: 0 ether
undefined
```

### 4.5 发送交易

发送交易时, 需要先对发起交易的账户进行 __解锁__:

```bash
> personal.unlockAccount(eth.accounts[0])
Unlock account 0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593
Passphrase:
true
```

这里从 __账户0__ 转10个以太币到 __账户1__ 中:

```bash
> amount = web3.toWei(10, 'ether')
"10000000000000000000"
> eth.sendTransaction({from: eth.accounts[0], to: eth.accounts[1], value: amount})
"0xdc6e22cf55db26a14486375e278712af8a19667f4541a8cca3d7ad67fcb5fad7"
```

我们去查看所有账户中的余额:

```bash
> checkAllBalances()
0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593:	balance: 890 ether
0xe74fc1abfbac8f598a979b11f23177f69f4de6de:	balance: 0 ether
undefined
```

发现还没转过来, 因为此时交易虽然已经提交到网络中, 但还未被处理.  
我们需要启动挖矿, 然后等一会挖到区块后, 才能够查看到交易完成后的信息:

```bash
> miner.start(2)
null
> miner.stop()
true
> checkAllBalances()
0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593:	balance: 905 ether
0xe74fc1abfbac8f598a979b11f23177f69f4de6de:	balance: 10 ether
```

> 注意: 这里的账户0中的余额变多了, 因为挖矿一段时间后, 会有新的以太币产生.

### 4.6 查看挂起的交易

```bash
> eth.pendingTransactions
[{
    blockHash: null,
    blockNumber: null,
    from: "0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593",
    gas: 90000,
    gasPrice: 18000000000,
    hash: "0xdc6e22cf55db26a14486375e278712af8a19667f4541a8cca3d7ad67fcb5fad7",
    input: "0x",
    nonce: 0,
    r: "0x74796e42c71cf051c3a078e56524ff2e4c26ebdef240c4f79a08efc931e1dcbd",
    s: "0x355af7b6a46d059cc53f73c878eadd59e0cede1131a7faa78e67e70913470c04",
    to: "0xe74fc1abfbac8f598a979b11f23177f69f4de6de",
    transactionIndex: 0,
    v: "0x42",
    value: 10000000000000000000
}]
```

如果返回值为空, 表示交易已全部完成:

```bash
> eth.pendingTransactions
[]
```

### 4.7 查看区块信息

查看当前区块总数:

```bash
> eth.blockNumber
183
```

查看某个区块信息:

```bash
> eth.getBlock(0)
{
  difficulty: 1024,
  extraData: "0x",
  gasLimit: 4294967295,
  gasUsed: 0,
  hash: "0x2720038ef46044a7a895296b85745294340ecfcedc32f8c9e9802129aeb62890",
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  miner: "0x0000000000000000000000000000000000000000",
  mixHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
  nonce: "0x0000000000000042",
  number: 0,
  parentHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
  receiptsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  size: 507,
  stateRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  timestamp: 0,
  totalDifficulty: 1024,
  transactions: [],
  transactionsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  uncles: []
}
> eth.getBlock(1)
{
  difficulty: 131072,
  extraData: "0xd98301080a846765746888676f312e31302e328664617277696e",
  gasLimit: 4290772993,
  gasUsed: 0,
  hash: "0x0dec1b5cb7304eea1bdd1d2d5ee73da53447ef54a4283557cea84025080d6d09",
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  miner: "0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593",
  mixHash: "0xf5ccd28f1816ecb5d3315811eca6273f913a86c4b1862174ce03fbb8c7f27a6b",
  nonce: "0x1d781ecfc8e4b9cb",
  number: 1,
  parentHash: "0x2720038ef46044a7a895296b85745294340ecfcedc32f8c9e9802129aeb62890",
  receiptsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  size: 538,
  stateRoot: "0xf336d1c5cab05b1015a033c502a47e02b7cacb41f055363fc7d59f8db2cb9a47",
  timestamp: 1528358068,
  totalDifficulty: 132096,
  transactions: [],
  transactionsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  uncles: []
}
> eth.getBlock(1000)
null
```

> `getBlock` 方法传入的是区块的序号数, 从0开始计数. 当区块不存在时, 将返回 `null`.

### 4.8 查看交易信息

```bash
> eth.getTransaction("0xdc6e22cf55db26a14486375e278712af8a19667f4541a8cca3d7ad67fcb5fad7")
{
  blockHash: "0x6dbf24bcecaad828ca1028209918fdeaa0eb54199a49f5d949823da3d8661fa8",
  blockNumber: 179,
  from: "0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593",
  gas: 90000,
  gasPrice: 18000000000,
  hash: "0xdc6e22cf55db26a14486375e278712af8a19667f4541a8cca3d7ad67fcb5fad7",
  input: "0x",
  nonce: 0,
  r: "0x74796e42c71cf051c3a078e56524ff2e4c26ebdef240c4f79a08efc931e1dcbd",
  s: "0x355af7b6a46d059cc53f73c878eadd59e0cede1131a7faa78e67e70913470c04",
  to: "0xe74fc1abfbac8f598a979b11f23177f69f4de6de",
  transactionIndex: 0,
  v: "0x42",
  value: 10000000000000000000
}
> eth.getTransaction("0xdc6e22cf55db26a14486375e278712af8a19667f4541a8cca3d7ad0000000000")
null
```

> `getTransaction` 方法传入的是交易的Hash值. 当交易不存在时, 将返回 `null`.

### 4.9 查看支持的智能合约编译器

```bash
> eth.compile
{
  lll: function(),
  serpent: function(),
  solidity: function()
}
```

<br/>

## 5. net 管理

### 5.1 查看网络监听状态
```bash
> net.listening
true
```

### 5.2 查看网络的Peer节点数
```bash
> net.peerCount
1
```

### 5.3 查看网络的id
```bash
> net.version
"15"
```

> 这里有个疑问: `version` 字面上返回的是 `networkid` 的值, 而不是什么版本值? 
> 很可能节点网络的版本用的就是 `networkid` 的值

<br/>

## 6. txpool 管理

### 6.1 查看交易内存池状态
```bash
> txpool.status
{
  pending: 1,
  queued: 0
}
```

### 6.2 查看交易内存池的内容
```bash
> txpool.content
{
  pending: {
    0x4378f11e3D4a1E26381CfD285f3237D4B7fc5593: {
      1: {
        blockHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
        blockNumber: null,
        from: "0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593",
        gas: "0x15f90",
        gasPrice: "0x430e23400",
        hash: "0x67738610c99674396e9474b099fce96de3d14df706fc966659b511fb1834195a",
        input: "0x",
        nonce: "0x1",
        r: "0xb8dbd2a8f28a3109f23150450df7c7811e564ddd428c49af55b405ce4c28e2ab",
        s: "0x59c2df6027bdfdc21ffe641f5e498c34089ae45f5daab323f34a02e3e2f67b5a",
        to: "0xe74fc1abfbac8f598a979b11f23177f69f4de6de",
        transactionIndex: "0x0",
        v: "0x41",
        value: "0x1158e460913d00000"
      }
    }
  },
  queued: {}
}
```

<br/>

## 7. web3 工具方法

在介绍 web3 对象中的工具方法前, 先要对 Ether 币的单位进行说明.  
Ether 币的最小单位为 wei, 也是命令行中默认的单位. 然后每1000个 wei 进一个单位, 依次为:

- kwei (1000 wei)
- mwei (1000 kwei)
- gwei (1000 mwei)
- szabo (1000 gwei)
- finney (1000 szabo)
- ether (1000 finney)

也即: __1 ether = 10^18 wei__

### 7.1 其它单位转换为 wei
```bash
> web3.toWei(1, 'ether')
"1000000000000000000"
> web3.toWei(1, 'kwei')
"1000"
> web3.toWei(1, 'szabo')
"1000000000000"
> web3.toWei(10)
"10000000000000000000"
> web3.toWei(10, null)
"10000000000000000000"
```

> 当 `toWei` 方法的第二个参数不传或者为 `null` 时, 默认将按 `ether` 单位进行转换为 wei.

### 7.2 wei 转换为其它单位
```bash
> web3.fromWei(1000000000000000000, 'ether')
"1"
> web3.fromWei(1000, 'kwei')
"1"
> web3.fromWei(1000000000000, 'szabo')
"1"
> web3.fromWei(10000000000000000000)
"10"
> web3.fromWei(10000000000000000000, null)
"10"
```

> 当 `fromWei` 方法的第二个参数不传或者为 `null` 时, 默认将转换为单位 `ether`.

### 7.3 判断是否为地址
```bash
> eth.accounts[0]
"0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593"
> web3.isAddress("0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593")
true
> web3.isAddress(0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593)
false
> web3.isAddress("0x4378f11e3d4a1e26381cfd285f3237d4b7fc5593000000")
false
> web3.isAddress()
false
> web3.isAddress(12345678)
false
```

> 当传入的是一个160位长的字符串数字时, 才返回 `true`. 否则返回 `false`.

