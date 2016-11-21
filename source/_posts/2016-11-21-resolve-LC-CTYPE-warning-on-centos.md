---
title: 解决 CentOS 中有关 LC_CTYPE warning 的问题
date: 2016-11-21 16:13:20
tags: [CentOS, linux]
categories:

---

解决使用终端登录 CentOS 系统时, 提示以下警示的问题:

> -bash warning: setlocale: LC_CTYPE: cannot change locale (UTF-8): No such file or directory

问题相关 [链接](https://gist.github.com/jampajeen/841d4eed4da3e6c758bf)

<!-- more -->
打开文件: `/etc/environment`

``` bash
$ vim /etc/environment
```

添加以下配置:

``` bash
LANG=en_US.UTF-8
LC_ALL=en_US.UTF-8
```

使配置生效(或者退出终端重新登录系统):

``` bash
$ source /etc/environment
```

> 如果以上步骤都还没效果, 可以重启 CentOS 系统试下. 
