---
title: 手动安装 Xcode simulator
date: 2016-10-26 13:04:02
tags: [Xcode, iOS]
categories:
---

> 背景: 对于 iOS 开发者来说, 每次新安装/升级了 Xcode 后, 经常会遇到缺少某些版本的 Simulator 问题. 对于需要进行不同 iOS 系统版本测试的开发者来说, 这是一件很不方便的事. 当然, 我们可以在 Xcode 中重新下载所缺的 Simulator. 但, 在我们伟大的天朝网络环境下, 加上 Apple 服务器那卡卡的访问速度, 我只想用两个字来形容--"呵呵"~

__如果我们能手动地直接下载 Simulator 来安装, 那就好了!__  

对于这个问题, 万能的 stackoverflow 是 [这样](http://stackoverflow.com/questions/29058229/download-xcode-simulator-directly) 回答的.

#### 1. 打开 Xcode, 打开 Preferences, 切换到 Components 选项卡.
<!-- more -->

![](http://ofn2gftwa.bkt.clouddn.com/xcode-install-simulators-manually-image-001.png?imageView2/2/800)

#### 2. 这时, 打开系统的 Console App, 点击左上角的 "Clear Display", 清空当前的 Console 信息.
![](http://ofn2gftwa.bkt.clouddn.com/xcode-install-simulators-manually-image-002.png?imageView2/2/800)

#### 3. 回到打开的 Xcode Preferences 窗口, 点击开始下载模拟器, 然后再取消它.

#### 4. 现在, 在 Console 窗口中, 你将会看到因取消操作而打印出的 Console 消息, 注意其中包含了完整的模拟器下载 URL.
![](http://ofn2gftwa.bkt.clouddn.com/xcode-install-simulators-manually-image-003.png?imageView2/2/1000)

#### 5. 从 Console 中复制出这个 URL 地址. 然后, 使用任意的下载工具去下载它(比如: 使用迅雷).
  > 注意, 这个 URL 在 Console 中输出时, 后面多了一个 `.` 字符. 复制出来时, 记得把这个多余的字符去掉. 
  > 如: 这里是 `https://xxxxxx.dmg.` 的型式, 需保留为 `https://xxxxxx.dmg`

![](http://ofn2gftwa.bkt.clouddn.com/xcode-install-simulators-manually-image-004.png?imageView2/2/800)

#### 6. 最后, 把下载下来的 `dmg` 文件, 复制到以下的文件夹路径下(若 Downloads 文件夹不存在, 手动创建它).

  ```shell
  ~/Library/Caches/com.apple.dt.Xcode/Downloads
  ```
> 注意: 一定不能修改下载文件的文件名
  
![](http://ofn2gftwa.bkt.clouddn.com/xcode-install-simulators-manually-image-005.png?imageView2/2/800)

#### 7. 回到 Xcode 中, 再次点击下载模拟器, 它将会自动查找到你下载的文件, 然后再安装它. 这一步就快很多了.
![](http://ofn2gftwa.bkt.clouddn.com/xcode-install-simulators-manually-image-006.png?imageView2/2/800)

