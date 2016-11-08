---
title: 使用 pip 更新库版本时, 报错 ReadTimeoutError 的解决办法
date: 2016-11-08 20:09:09
tags: [python, pip]
categories:

---


今天, 我本想更新一下 `Flask` 的版本. 于是, 我先运行下面的升级命令: 

``` bash
$ sudo pip install --upgrade flask
```

刚开始时, 它下载 `Flask` 的更新库是没问题的, 但在继续下载 `Flask` 依赖的 `Jinja2` 库是时, 却提示出错了:

<!-- more -->

![](http://ofn2gftwa.bkt.clouddn.com/pip-upgrade-raise-read-timeout-error-001.png)

一眼看上去, 吸引我注意力的不是那一大片的红色错误, 却是最下面的这个提示:

``` bash
You are using pip version 8.1.2, however version 9.0.1 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
```

`pip` 有新版本了, 赶紧升级一下! 于是, 我兴高采烈地运行下面的命令:

``` bash
& pip install --upgrade pip
```

可它刚下载了一会, 却又报了一大片红:

![](http://ofn2gftwa.bkt.clouddn.com/pip-upgrade-raise-read-timeout-error-002.png)

同样的错误: `ReadTimeoutError: HTTPSConnectionPool(host='pypi.python.org', port=443): Read timed out.`
我想应该是网络不给力了. 于是, 我把网络代理从 `Auto Proxy Mode` 直接切换成了 `Global Mode`. 然后, 又重新运行了一遍更新命令. 可是问题依旧.

八达岭内的网络环境, 真的是博大精深~

我只好打开 [Google](https://www.google.com) 求助. 解决方法还挺简单的, 此处借用一下:
http://angelzou.github.io/06-08-2015-update-pip-with-read-timeout-error.html

## 解决方法:
* 到 https://pypi.python.org/simple/pip/ 下载最新的 `whl` 文件进行手动更新.
* 加大超时时间:

  ``` bash
  $ pip --default-timeout=100 install -U pip
  ```

先试下第一种方法, 打开 https://pypi.python.org/simple/pip/ , 搜索到 `pip` 的最新版本 `9.0.1`. 然后下载到本地.

![](http://ofn2gftwa.bkt.clouddn.com/pip-upgrade-raise-read-timeout-error-003.png)

可下载下来的是 `whl` 文件. 这个格式的文件怎么安装呢? 我先双击它一下试试, 没反应. 又去 [Google](https://www.google.com) 了一下关键字 `pip whl`.

万能的 stackoverflow 是 [这样](http://stackoverflow.com/questions/27885397/how-do-i-install-a-python-package-with-a-whl-file) 说的.
原来直接使用 `pip install <whl-filename>` 来安装就可以了:

![](http://ofn2gftwa.bkt.clouddn.com/pip-upgrade-raise-read-timeout-error-004.png)

`pip` 是升级完成了, 可我最初想升级的 `Flask` 还没好呢. 我想这时可以试一下第二种方法了. 于是, 我又运行下面的命令:

``` bash
pip --default-timeout=100 install -U flask
```

可是, 网络还是不给力啊! 我重试了几次都还是报下面的错误:

![](http://ofn2gftwa.bkt.clouddn.com/pip-upgrade-raise-read-timeout-error-005.png)

看来国外的原始源地址怕是靠不住了, 还是找个国内的镜像源吧. 我又去搜索了一翻. 还真找到个国内的镜像源. 详情看这里: http://blog.csdn.net/u010536377/article/details/50564185 (顺便感谢一下[中国科学技术大学](http://www.ustc.edu.cn))

我又兴高采烈地使用新的镜像源, 运行下面的命令: 

``` bash
$ pip install --upgrade --index https://pypi.mirrors.ustc.edu.cn/simple/ flask
```

那个下载速度真是秒开! 可是, 安装的时候却 `Permission denied:` 了:

![](http://ofn2gftwa.bkt.clouddn.com/pip-upgrade-raise-read-timeout-error-006.png)

赶紧加上 `sudo` 再来一次. 这次是完美的完成任务了:

![](http://ofn2gftwa.bkt.clouddn.com/pip-upgrade-raise-read-timeout-error-007.png)

<br/>
> 其实, 在升级 Flask 时, 我们也可以像第一种方法那样, 在 https://pypi.python.org/simple/flask/ 中下载最新的 `Flask` 对应的 `whl` 文件到本地, 进行手动的更新.
> 但是, 程序员都是喜欢折腾的啊~