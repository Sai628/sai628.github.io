---
title: Nginx 配置代码片段
date: 2019-08-15 10:36:25
tags: [Nginx]
categories:
---

## 请求重定向到新域名(使用 return 指令)

```bash
server {
    listen 80;
    listen 443 ssl;
    server_name www.old-name.com old-name.com;
    return 301 $scheme://www.new-name.com$request_uri;
}
```

> NGINX VARIABLES:
>
> $scheme: request scheme, "http" or "https".  
> $request_uri: full original request URI(with arguments).

<!--more-->

<br/>

## 请求的部分路径进行重定向(使用 rewrite 指令)

```bash
server {
    # ...
    rewrite ^(/download/.*)/media/(\w+)\.?.*$ $1/mp3/$2.mp3 last;
    rewrite ^(/download/.*)/audio/(\w+)\.?.*$ $1/mp3/$2.ra last;
    return 403;  # 最后返回403, 表示若URL没有匹配到上面的任意规则时, 返回403错误码给请求端
    # ...
}
```

> 以上配置重定向demo:
>
> /download/cdn-west/media/file1 -> /download/cdn-west/mp3/file1.mp3  
> /download/cdn-west/media/file2.flv -> /download/cdn-west/mp3/file2.mp3  
> /download/cdn-est/audio/file3 -> /download/cdn-west/mp3/file3.ra

<br/>

## 请求尝试性的进行重定向(使用 try_files 指令)

```bash
location /images/ {
    try_files $uri $uri/ /images/default.gif;
}

location = /images/default.gif {
    expires 30s;
}
```

> 以上配置重定向尝试demo:
>
> 假设请求URL为: http://www.domain.com/images/image1.gif,
>  
> 将先尝试寻找文件: http://www.domain.com/images/image1.gif,  
> 不存在时, 尝试重定向到目录: http://www.domain.com/images/image1.gif/  
> 再不存在时, 最后重定向到路径: http://www.domain.com/images/default.gif
>
> 下一个 location 规则, 表示匹配该条件时, 设置文件缓存时间为30秒.

<br/>

## 添加与移除 www 前缀

```bash
# add "www" prefix
server {
    listen 80;
    listen 443 ssl;
    server_name domain.com;
    return 301 $scheme.www.domain.com$request_uri;
}

# remove "www" prefix
server {
    listen 80;
    listen 443 ssl;
    server_name www.domain.com;
    return 301 $scheme://domain.com$request_uri;
}
```

<br/>

## 重定向全部流量至正确的域名

```bash
server {
    listen 80 default_server;
    listen 443 ssl default_server;
    server_name _;
    return 301 $scheme://www.domain.com;
}
```

<br/>

## 强制全部请求都使用 SSL/TLS

```bash
server {
    listen 80;
    server_name www.domain.com;
    return 301 https://www.domain.com$request_uri;
}
```

<br/>

## 让 WordPress 网站启动美化的永久链接

```bash
location / {
    try_files $uri $uri/ /index.php?$args;
}
```

<br/>

## 阻止请求中不支持的文件拓展

```bash
location ~ .(aspx|php|jsp|cgi)$ {
    return 410;
}
```

```bash
location ~ .(aspx|php|jsp|cgi)$ {
    deny all;
}
```

<br/>

## 配置自定义的重路由

```bash
rewrite ^/listings/(.*)$ /listing.html?listing=$1 last;
```
