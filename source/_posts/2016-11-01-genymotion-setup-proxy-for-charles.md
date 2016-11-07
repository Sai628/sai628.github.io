---
title: Genymotion 配合 Charles 进行网络抓包
date: 2016-11-01 23:28:57
tags: [charles, genymotion]
categories:

---


> [Charles](https://www.charlesproxy.com) 是在 Mac 下常用的网络封包截取工具. 在做移动开发时, 我们为了调试与服务器端的网络通讯协议, 常常需要截取网络封包来分析. Charles 通过将自己设置成系统的网络访问代理服务器, 使得所有的网络访问请求都通过它来完成, 从而实现了网络封包的截取和分析.

> [Genymotion](http://www.genymotion.net) 是最新版的 Android 模拟器. 它使用 x86 架构, 因此更加高效, 加载 APP 的速度很快, 操作起来也很流畅. 借助 OpenGL 硬件加速的优势, 它可以让您以惊人的 3D 性能测试您的应用程序.

在进行 Android 开发时, 有些需要抓取应用的网络封包进行 API 分析. 利用 Genymotion 配合 Charles, 就可以很好的完成这一工作. 而这需要先进行一些设置.

<!-- more -->

##### 1. 在 Genymotion 模拟器中, 打开 Settings -> Wi-Fi. 长按已连接的 WiFi 的 SSID (这里是 WiredSSID). 打开如下的选择菜单列表, 然后点击 "Modify network":
![](http://ofn2gftwa.bkt.clouddn.com/genymotion-setup-proxy-for-charles-image-001.png?imageView2/2/h/500)

##### 2. 然后勾选上 "Show advanced options".
![](http://ofn2gftwa.bkt.clouddn.com/genymotion-setup-proxy-for-charles-image-002.png?imageView2/2/h/500)

##### 3. 在新出现的菜单项中, "Proxy" 一项选择 "Manual". "Proxy hostname" 填写 "10.0.3.2", "Proxy port" 填空 "8888"(我这里的 Charles 中设置的 HTTP Proxy 为 8888). 然后 Save 即可.
![](http://ofn2gftwa.bkt.clouddn.com/genymotion-setup-proxy-for-charles-image-003.png?imageView2/2/h/500)
![](http://ofn2gftwa.bkt.clouddn.com/genymotion-setup-proxy-for-charles-image-004.png?imageView2/2/w/600)

##### 4. 最后, 您将会看到 Genymotion 中的网络封包情况出现了在 Charles 中.
![](http://ofn2gftwa.bkt.clouddn.com/genymotion-setup-proxy-for-charles-image-005.png?imageView2/2/h/500)
![](http://ofn2gftwa.bkt.clouddn.com/genymotion-setup-proxy-for-charles-image-006.png?imageView2/2/h/500)