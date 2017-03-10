---
title: Git 保证 fork 后的项目与原项目同步更新
date: 2016-03-10 00:25:42
tags: [git]
categories:
---


当在github上fork出一个项目后, 该如何保持fork出的项目与上游的原始项目保持更新呢?  
可以通过以下的步骤实现:

__1. 首先, 需要在 fork 出的代码仓库中添加上游代码库(即原始项目)的 remote 源:__

``` bash
$ git remote add {upstream} https://github.com/Sai628/cocos2d-x.git
```

其中`upstream`为上游代码仓库的名字，可以自由设置.

<!-- more -->

__2. 提交本地的修改:__

``` bash
$ git commit -m ""
```
	
__3. 确保将本地代码库切换至待同步的分支:__

``` bash
$ git checkout {branch_name}
```

__4. 更新上游的远程代码库，并与本地的代码库进行同步:__

``` bash
$ git remote update {upstream}
$ git rebase {upstream}/{branch_name}
```

或者是直接运行 `git pull {upstream}/{branch_name}` 从上游代码库中拉取即可.

__5. 将本地代码库更新到你的远程仓库去:__

``` bash
$ git push
```
    
