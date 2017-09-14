---
title: CentOS 7 配置防火墙
date: 2017-09-14 14:38:52
tags: [CentOS, linux]
categories:
---

```bash
$ systemctl { enable | start | stop | restart | status } firewalld  # 防火墙 启用|运行|停止|重启|查看状态

$ firewall-cmd --state  # 检查防火墙状态
$ firewall-cmd --list-all  # 查看防火墙配置信息
$ firewall-cmd --zone=public --add-port=80/tcp --permanent    # 永久开放80端口TCP协议
$ firewall-cmd --zone=public --add-port=5000/udp --permanent  # 永久开放5000端口UDP协议
$ firewall-cmd --reload  # 重新加载防火墙配置
```
