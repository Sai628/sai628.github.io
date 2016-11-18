---
title: HTTP 常用首部字段一览表
date: 2016-11-17 22:41:26
tags: [http]
categories:

---

> 通用首部字段

|首部字段名|说明|
|:---|:---|
|Cache-Control|控制缓存的行为|
|Connection|逐跳首部, 连接的管理|
|Date|创建报文的日期时间|
|Pragna|报文指令|
|Trailer|报文末端的首部一览|
|Transfer-Encoding|指定报文主体的传输编码方式|
|Upgrade|升级为其它协议|
|Via|代理服务器的相关信息|
|Warning|错误通知请求首部字段|
<br/>

<!-- more -->

> 请求首部字段

|首部字段名|说明|
|:---|:---|
|Accept|用户代理可处理的媒体类型|
|Accept-Charset|优先的字符集|
|Accept-Encoding|优先的内容编码|
|Accept-Language|优先的语言(自然语言)|
|Authorization|Web认证信息|
|Expect|期待服务器的指定行为|
|From|用户的电子邮箱地址|
|Host|请求资源所在服务器|
|if-Match|比较实体标记(ETag)|
|if-Modified-Since|比较资源的更新时间|
|if-None-Match|比较实体标记(与if-Match相反)|
|if-Range|资源为更新时发送实体Byte的范围请求|
|if-Unmodified-Since|比较资源的更新时间(与if-Modified-Since相反)|
|Max-Forwards|最大传输逐跳数|
|Proxy-Authorization|代理服务器要求客户端的认证信息|
|Range|实体字节范围请求|
|Referer|对请求中的URL的原始获取方法|
|TE|传输编码的优先级|
|User-Agent|HTTP客户端程序的信息|
<br/>

> 响应首部字段

|首部字段名|说明|
|:---|:---|
|Accept-Ranges|是否接受字节范围请求|
|Age|推算资源创建经过时间|
|ETag|资源的匹配信息|
|Location|令客户端重定向至指定的URL|
|Proxy-Authenticate|代理服务器对客户端的认证信息|
|Rety-After|对再次发起请求的时机要求|
|Server|HTTP服务器的安装信息|
|Vary|代理服务器缓存的管理信息|
|WWW-Authenticate|服务器对客户端的认证信息|
<br/>

> 实体首部字段

|首部字段名|说明|
|:---|:---|
|Allow|资源支持的HTTP方法|
|Content-Encoding|实体主体适用的编码方式|
|Content-Language|实体主体的自然语言|
|Content-Length|实体主体的大小(单位:字节)|
|Content-Location|替代资源的URL|
|Content-MD5|实体主体的报文摘要|
|Content-Range|实体主体的位置范围|
|Content-Type|实体主体的媒体类型|
|Expires|实体主体过期的日期时间|
|Last-Modified|资源的最后修改日期时间|
|为Cookie服务的首部字段|-|
<br/>

> Cookie服务首部字段

|首部字段名|说明|
|:---|:---|
|Set-Cookie|开始状态管理所有的Cookie信息|
|Cookie|服务器接收到的Cookie信息|
<br/>

> Set-Cookie字段属性

|属性|说明|
|:---|:---|
|NAME=VALUE|赋予Cookie的名称和其值|
|expires=DATE|Cookie的有效期(若不明确指定则默认为浏览器关闭前为止)|
|path=PATH|将服务器上的文件目录作为Cookie的适用对象(若不指定则默认为文档所在的目录)|
|domain=域名|作为Cookie适用对象的域名(若不指定则默认为创建Cookie的服务器的域名)|
|Scure|仅在HTTPS安全通信时才会发送Cookie|
|HttpOnly|加以限制, 使Cookie不能被JavaScript脚本访问|
