---
title: 在 CentOS 或 Ubuntu 中添加 Sudo User
date: 2017-12-25 20:14:54
tags: [CentOS, Ubuntu, linux]
categories:
---


## 介绍
在 `Linux` 系统中, 一般对于非 `root` 用户, 想要使用管理员权限去运行一些命令的话, 可以在命令的前面加上 `sudo`(这要求当前运行命令的用户能够具有 `sudo` 的使用权).

本文将介绍一种很简单的方式去创建具有 `sudo` 使用权的系统新用户, 并且不需要修改你服务器上的 `sudoers` 文件. 若你想直接对已存在的用户配置 `sudo` 的使用权, 可以直接跳到步骤3.

<!-- more -->
<br/>

## 创建 Sudo User 步骤

#### 1. 使用 `root` 用户登录你的服务器
```bash
local$ ssh root@server_ip_address
```

#### 2. 使用 `adduser` 命令, 在系统中添加一个新的用户
```bash
$ adduser [user-name]
```

  > `Ubuntu` 会在运行 `adduser` 时, 提示你去设置密码;  
  > 而在 `CentOS` 中, 需要使用 `passwd` 命令修改新用户的密码:

```bash
$ passwd [user-name]  # for CentOS
```

#### 3. 使用 `usermod` 命令将新创建的用户添加到具有 `sudo` 权限的组中
```bash
$ usermod -aG wheel [user-name]  # for CentOS
$ usermod -aG sudo [user-name]  # for Ubuntu
```

  > 在 `CentOS` 中, 默认 `wheel` 组的成员具有 `sudo` 权限;
  > 而在 `Ubuntu` 中, 则是 `sudo` 组的成员.

#### 4. 在新的用户账户中, 测试 `sudo` 使用权

  - 使用 `su` 命令切换到新用户的账户
  ```bash
  $ su - [user-name]
  ```

  - 作为新用户, 在你想运行命令的前面添加上 `sudo`, 去检测是否可以使用超级用户权限去运行
  ```bash
  username$ sudo command_to_run
  ```

  - 例如, 你可以尝试显示 `/root` 目录下的内容, 一般这仅对于 `root` 用户才可以访问
  ```bash
  username$ sudo ls -lah /root
  ```