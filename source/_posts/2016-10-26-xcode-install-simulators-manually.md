---
title: 手动安装 Xcode simulator
date: 2016-10-26 13:04:02
tags: [Xcode, iOS]
categories:

---


> 对于 iOS 开发者来说, 每次重新安装/升级了 Xcode 后, 经常会遇到缺少某些版本的 Simulator 问题. 对于需要进行不同 iOS 系统版本测试的开发者来说, 这是一件很不方便的事.   
> 当然, 我们可以在 Xcode 中重新下载所缺的 Simulator. 但是, 由于八达岭内网络环境的复杂性, 我们需要另辟蹊径.

__如果我们能手动地直接下载 Simulator 来安装, 那就好了!__
对于这个问题, 万能的 __stackoverflow__ 是 [这样](http://stackoverflow.com/questions/29058229/download-xcode-simulator-directly) 回答的.

##### 1. 打开 Xcode, 打开 Preferences, 切换到 Components 选项卡.
<!-- more -->

![alt text][image-001]

##### 2. 这时, 打开系统的 Console App, 点击左上角的 "Clear Display", 清空当前的 Console 信息.
![alt text][image-002]

##### 3. 回到打开的 Xcode Preferences 窗口, 点击开始下载模拟器, 然后再取消它.

##### 4. 现在, 在 Console 窗口中, 你将会看到因取消操作而打印出的 Console 消息, 注意其中包含了完整的模拟器下载 URL.
![alt text][image-003]

##### 5. 从 Console 中复制出这个 URL 地址. 然后, 使用任意的下载工具去下载它(比如迅雷).

![alt text][image-004]

> 注意, 这个 URL 在 Console 中输出时, 后面多了一个 `.` 字符. 复制出来时, 记得把这个多余的字符去掉. 
> 如: 这里是 `https://xxxxxx.dmg.` 的型式, 需修改为 `https://xxxxxx.dmg`

##### 6. 最后, 把下载下来的 `dmg` 文件, 复制到以下的文件夹路径下(若 Downloads 文件夹不存在, 手动创建它).

```shell
~/Library/Caches/com.apple.dt.Xcode/Downloads
```
  
![alt text][image-005]

> 注意: 一定不能修改下载文件的文件名

##### 7. 回到 Xcode 中, 再次点击下载模拟器, 它将会自动查找到你下载的文件, 然后再安装它. 这一步就快很多了.
![alt text][image-006]


[image-001]: http://sai628-blog-image.oss-cn-shenzhen.aliyuncs.com/xcode-install-simulators-manually-image-001.png?x-oss-process=image/resize,l_800
[image-002]: http://sai628-blog-image.oss-cn-shenzhen.aliyuncs.com/xcode-install-simulators-manually-image-002.png?x-oss-process=image/resize,l_800
[image-003]: http://sai628-blog-image.oss-cn-shenzhen.aliyuncs.com/xcode-install-simulators-manually-image-003.png?x-oss-process=image/resize,l_1000
[image-004]: http://sai628-blog-image.oss-cn-shenzhen.aliyuncs.com/xcode-install-simulators-manually-image-004.png?x-oss-process=image/resize,l_800
[image-005]: http://sai628-blog-image.oss-cn-shenzhen.aliyuncs.com/xcode-install-simulators-manually-image-005.png?x-oss-process=image/resize,l_800
[image-006]: http://sai628-blog-image.oss-cn-shenzhen.aliyuncs.com/xcode-install-simulators-manually-image-006.png?x-oss-process=image/resize,l_800
