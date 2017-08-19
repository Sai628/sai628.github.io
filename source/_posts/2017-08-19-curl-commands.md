---
title: curl 命令参考
date: 2017-08-19 16:27:09
tags: [shell, linux]
categories:
---

|选项|作用|
|--|--|
|\-\-cookie|指定请求的 cookie 数据. 例如: "name=Sai"|
|-d|指定请求数据|
|\-\-data-binary|指定发送的文件|
|-H|指定请求头<br/> 例如: "Content-type:application/json"|
|-i|显示响应头部信息|
|-o|将请求的内容保存为文件|
|\-\-referer|指定请求的 Referer 来源|
|-u|指定认证用户名与密码. 例如: name:password|
|\-\-user-agent|指定请求的 User Agent 信息|
|-v|显示一次 HTTP 通信的整个过程, <br/>包括端口连接和 HTTP Request 头信息|
|-X|指定 HTTP 请求方法. 例如: POST, GET, PUT|

<!-- more -->
<br/>

### Demo:
```bash
$ curl [URL]  # 默认为 GET 方式请求
$ curl --cookie "name=Sai" [URL]  # 添加 cookie 数据
$ curl -X POST -d "data=xxx" [URL]  # 以 POST 方式请求, 并添加请求数据 data=xxx
$ curl -H "Content-Type:application/json" [URL]  # 添加 header 信息
$ curl -i [URL]  # 显示请求响应的头部内容, 连同请求响应内容.
$ curl -o [file_name] --progress [URL]  # 将请求的内容保存到文件中, 并显示下载进度
$ curl --referer [REF_URL] [URL]  # 设置请求来源地址
$ curl --user-agent "[User Agent]" [URL]  # 设置请求的 User Agent 信息
```
