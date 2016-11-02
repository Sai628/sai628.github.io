---
title: Hexo常用命令总结
date: 2016-08-15 00:12:24
tags: [hexo]
categories:
---



## init
```bash
hexo init [folder]
```
 
新建一个网站.如果没有设置 `folder`,Hexo默认在当前的文件夹下建立网站.
<!--more-->


## new
```bash
hexo new [layout] <title>
```

新建一篇文件.如果没有设置 `layout` 的话,默认使用 `_config.yml` 文件中的 `default_layout` 参数代替.如果标题 `title` 包含空格的话,需要使用引号括起来.
例如:  

```bash
hexo new "my post"
```

## generate
```bash
hexo generate  #可使用简写: hexo g
```

生成静态文件.

|选项|描述|
|--|--|
|`-d, --deploy`|文件生成后立即部署网站|
|`-f, --force`|强制重新生成|
|`-w, --watch`|监视文件变动|


# publish
```bash
hexo publish [layout] <filename>
```

发表草稿.

# server
```bash
hexo server  #可使用简写: hexo s
```

启动服务器并监视文件变化.默认情况下,访问网址为 `http://0.0.0.0:4000/`

|选项|描述|
|--|---|
|`-i, --ip`|重设默认的服务器IP,默认绑定全部的IP地址|
|`-l, --log [format]`|启动日记记录,使用覆盖记录格式|
|`-o, --open`|立即在你的默认浏览器打开服务器的URL|
|`-p, --port`|重设端口|
|`-s, --static`|只使用静态文件|

# deploy
```bash
hexo deploy  #可使用简写: hexo d
```

部署网站.

|选项|描述|
|--|--|
|`--setup`|仅设置,不部署|
|`-g, --generate`|部署之前预先生成静态文件|

# clean
```bash
hexo clean
```

清除缓存文件 `(db.json)` 和已生成的静态文件 `(public)`.


# list
```bash
hexo list <type>
```

列出网站资料. `<type>` 的可选类型有: `page, post, route, tag`

# version
```bash
hexo version
```

显示Hexo版本.