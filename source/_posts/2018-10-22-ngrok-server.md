---
title: 自己编译 ngrok 源码以搭建 ngrokd 服务器
date: 2018-10-22 10:42:54
tags: [ngrok]
categories:
---

服务器系统版本: 

> Ubuntu 16.04.3 LTS

## 1. 安装 golang 等依赖库

```bash
$ add-apt-repository ppa:longsleep/golang-backports
$ apt update
$ apt install golang

$ apt install build-essential mercurial git
```

<br/>

<!-- more -->

## 2. 设置 golang 环境变量

在 ~/.bash_profile 或 ~/.zshrc 文件中, 添加如下代码:

```bash
export GOPATH=$HOME/go
PATH=$PATH:$HOME/.local/bin:$HOME/bin:$GOPATH/bin
```

使配置生效:

```bash
$ source ~/.bash_profile
```

<br/>

## 3. 下载 ngrok 项目源码

```bash
$ git clone https://github.com/inconshreveable/ngrok.git ngrok
```

<br/>

## 4. 生成自签名证书

使用 ngrok.com 官方服务时, 我们使用的是官方的 SSL 证书. 自建 ngrokd 服务, 如果不想购买 SSL 证书, 我们需要生成自己的自签名证书, 并编译一个携带该证书的 ngrok 客户端.

证书生成过程需要一个 NGROK_BASE_DOMAIN. 以 ngrok 官方随机生成的地址 xxx.ngrok.com 为例, 其 NGROK_BASE_DOMAIN 就是 "ngrok.com".  
如果你要提供服务的地址为 "example.ngrok.xxx.com", 那 NGROK_BASE_DOMAIN 就应该是 "ngrok.xxx.com".

这里设置 NGROK_BASE_DOMAIN 地址为 "ngrok.mywebsite.com".  
(请自行替换为自己拥有的域名)

```bash
$ cd ngrok  # 切换到 ngrok 项目源码文件夹

$ NGROK_DOMAIN="ngrok.mywebsite.com"

$ openssl genrsa -out base.key 2048
$ openssl req -new -x509 -nodes -key base.key -days 10000 -subj "/CN=$NGROK_DOMAIN" -out base.pem
$ openssl genrsa -out server.key 2048
$ openssl req -new -key server.key -subj "/CN=$NGROK_DOMAIN" -out server.csr
$ openssl x509 -req -in server.csr -CA base.pem -CAkey base.key -CAcreateserial -days 10000 -out server.crt
```

我们在编译可执行文件之前, 需要把生成的证书分别替换到 assets/client/tls 和 assets/server/tls 中，这两个目录分别存放着 ngrok 和 ngrokd 的默认证书。

```bash
$ cp base.pem assets/client/tls/ngrokroot.crt
$ cp server.crt assets/server/tls/snakeoil.crt
$ cp server.key assets/server/tls/snakeoil.key
```

<br/>

## 5. 编译服务端程序 ngrokd 与客户端程序 ngrok

```bash
$ make release-server  # 编译服务端程序 ngrokd
```

以下命令, 可编译指定系统平台与架构的客户端程序 ngrok:

```bash
$ GOOS=linux GOARCH=amd64 make release-client
$ GOOS=linux GOARCH=386 make release-client

$ GOOS=windows GOARCH=amd64 make release-client
$ GOOS=windows GOARCH=386 make release-client

$ GOOS=darwin GOARCH=amd64 make release-client
$ GOOS=darwin GOARCH=386 make release-client
```

<br/>

## 6. 启动 ngrokd 服务端

```bash
$ ngrokd -tlsKey=server.key -tlsCrt=server.crt -domain="ngrok.mywebsite.com" -httpAddr=":8088" -httpsAddr=":8089"
```

可通过 -tlsKey -tlsCrt 参数, 可指定服务端的证书. 不加该参数时, 会使用 ngrokd 二进制编译时的证书文件.  
httpAddr、httpsAddr 分别是 ngrokd 用来转发 http、https 服务的端口，可以随意指定.  
ngrokd 还会开一个 4443 端口用来跟客户端通讯（可通过 -tunnelAddr=”:xxx” 指定）.  

> 想要在后台运行 ngrokd 时, 可使用 nohup 启动.

<br/>

## 7. 设置 ngrokd 为系统程序, 并后台运行

服务器在运行 ngrokd 时, 如果关闭会话窗口, 会导致服务中断, 很显然这不是我们想要的结果, 我们需要服务不断的在后台运行, 当需要的时候在停止.

在 /etc/systemd/system/ 目录下创建服务 ngrokd.service，内容为:

```bash
[Unit]
Description=ngrokd
After=network.target

[Service]
ExecStart=/path/to/ngrokd -tlsKey=/path/to/server.key -tlsCrt=/path/to/server.crt -domain="ngrok.mywebsite.com" -httpAddr=":8088" -httpsAddr=":8089"

[Install]
WantedBy=multi-user.target
```

其中, 需根据自己的实际目录修改相应的目录, 这样我们就可通过 systemctl start ngrokd.service 启动服务.

<br/>

## 8. 启动 ngrok 本地客户端

先新建一个配置文件 ngrok.cfg, 内容以下:

```bash
server_addr: "ngrok.mywebsite.com:4443"  
trust_host_root_certs: false  
```

> server_addr 端口默认 4443, 可通过修改 ngrokd 启动时的参数去修改该端口

运行客户端, 暴露本地 4000 端口站点(默认为http协议):

```bash
$ ngrok -subdomain=demo -log=ngrok.log -config=ngrok.cfg 4000  # 暴露本地4000端口(默认http协议)

$ ngrok -proto=tcp -log=ngrok.log -config=ngrok.cfg 22  # 暴露本地22端口(tcp协议)
```


> 其中, -config 参数指定配置文件, -log 参数指定存放日志文件位置, -subdomain 参数为自定义的域名前缀. 4000 与 22 为端口号.  
> 当未指定协议时, 默认为 http. 可使用 -proto 参数指定协议(http/tcp/tls)

在浏览器中输入: demo.ngrok.mywebsite.com, 则可访问本地 4000 端口的站点内容.  
在 ngrok 客户端所在计算机的浏览器中, 通过 localhost:4040 可查看页面请求情况.

<br/>

## 9. 使用 screen 命令, 使后台运行 ngrok 客户端

```bash
$ screen -S [名字]  # 例如: keepngrok
```

然后运行 ngrok 启动命令.  
最后按快捷键 ctrl+A+D. 即可以保持 ngrok 后台运行

<br/>

## 10. 设置 ngrok 开机自启动

在 /etc/systemd/system/ 目录下, 创建一个名为 ngrok.service 的文件, 输入以下代码:

```bash
[Unit]
Description=ngrok
After=network.target

[Service]
Type=simple
ExecStart=/path/to/ngrok -subdomain=demo -log=/path/to/ngrok.log -config=/path/to/ngrok.cfg 4000

[Install]
WantedBy=multi-user.target
```

> 注意根据实际情况, 修改相应的路径名及参数值.

#### 10.1. 运行 ngrok 服务
```bash
$ systemctl start ngrok
```

#### 10.2. 查询 ngrok 服务的运行状态
```bash
$ systemctl status ngrok
```

#### 10.3. 实现开机自启动 ngrok 服务
```bash
$ systemctl enable ngrok
```

