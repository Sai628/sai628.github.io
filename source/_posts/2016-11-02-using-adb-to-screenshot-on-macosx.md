---
title: 使用 adb 命令进行截屏
date: 2016-11-02 19:28:46
tags: [android, adb]
categories:
---

在 MacOS 中, 利用 adb 命令进行截屏:

```bash
# 最后输出定位符 `>` 右边的参数为输出文件路径, 可任意指定 
adb shell screencap -p | perl -pe 's/\x0D\x0A/\x0A/g' > ~/Desktop/screenshot_$(date +%Y_%m_%d_%H_%M_%S).png
```

