---
title: GitLab 与 GitFlow 的使用与流程规范
date: 2016-11-02 20:41:58
tags: [git, gitlab, gitflow]
categories:
---


## 0x00 Git客户端
推荐使用 [SourceTree](https://www.sourcetreeapp.com). MacOS 与 Windows 平台均支持.
当然如果你觉得方便的话, 也可以直接命令行.


## 0x01 SSH Keys
通过 ssh 的方式提交代码, 可以免于每次都输入密码.  
在  gitlab 平台的 [Profile Settings](http://gitlab.libtop.com/profile/keys) 面板里可以添加 `SSH Keys`. 里面有相应的简单使用教程.

<!-- more -->

![](http://ofn2gftwa.bkt.clouddn.com/gitlab-gitflow-tutorial-image-2.png?imageView2/2/w/1000)

#### 注意: 解决本地多个 `SSH Keys` 的问题 

> 如果你之前在其它的 git 平台(比如 github )上添加过`ssh keys`. 在你的本机上可能已经存在 `id_rsa.pub` 文件. (若你是第一次生成 `SSH Keys`, 可以跳过本小节的以下内容)

1. 当你生成用于 gitlab 平台的 `ssh keys` 时, 运行以下命令:

  ``` bash
$ ssh-keygen -t rsa -C "your-email-address"
```

  在命令的提示中, 输入新的存储路径与文件名, 以避免覆盖已经存在的 `id_rsa.pub` 文件内容.
   
  也可以直接使用以下命令指定新的 `SSH Keys` 存储文件名. (my\_new\_rsa 可换成其它的名字)  

  ``` bash
$ ssh-keygen -t rsa -f ~/.ssh/my_new_rsa -C "your-email-address"
```

2. 添加 config 文件(若还不存在的话), 并配置新生成的 `rsa` 文件信息.  

  ``` bash
$ touch ~/.ssh/config
```

3. 在 config 文件中添加以下内容:  

  ``` bash
Host your_gitlab_website
User your_email_adddress
PreferredAuthentications publickey
IdentityFile ~/.ssh/my_new_rsa
```

   MacOS 用户也通过以下命令, 直接将 my\_new\_rsa 的信息添加到系统的钥匙串 keychain 中, 而不必配置 config 文件:
 
  ``` bash
$ ssh-add -K ~/.ssh/my_new_rsa
```

#### 测试是否配置成功:

``` bash
$ ssh -T git@gitlab.com
```

如果配置成功, 会显示类似以下的欢迎文字:  
> Welcome to GitLab, guys!


## 0x02 Git基础
[git - 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)
<br/>


## 0x03 GitFlow 与分支模型
![](http://ofn2gftwa.bkt.clouddn.com/gitlab-gitflow-tutorial-image-002.jpg)

出于 **保持分支模型简单** 与 **保证代码库稳定** 的考虑, 项目代码分支使用以上的分支模型.  
遵从以下的 GitFlow 约定:

1. ##### master 为稳定分支, develop 为开发分支, feature 为特性分支. 
   feature 分支以某功能特性来创建, 而不是以需求点.

2. ##### 仅有一个 master 分支与一个 develop 分支, 可以有0或多个 feature 分支.
   项目初始时, develop 分支由 master 分支创建得到, feature 分支由 develop 分支创建得到.

3. ##### master 分支仅用于发布正式版本, 该分支上的代码保持稳定, 并可以即时发包.
master 分支发包后, 须打上相关 `tag` 信息. 

4. ##### develop 分支为开发的分支. 其中包含最新的功能代码.
  - develop 分支上的代码应为完成了某些功能需求的开发,但可能未通过稳定性测试.   
  - 测试版本 beta 包从 develop 分支上发布, 用于测试或演示新功能.  
  - 一旦 develop 分支的代码通过稳定性测试, 就可合并到 master 分支中.(该工作由项目的管理者进行)

5. ##### feature 分支用于开发当前的新功能特性, 其上面的代码很可能极不稳定. 
  - feature 分支上以功能特性来命名, 比如 `feature/support-video`.  
  - 当不同的开发人员负责各不相同的功能点时, 最好每个人创建自己的 feature 的分支, 由自己来维护该 feature 的开发功能.  
  - 在新创建 feature 分支之前, 须先 pull 最新的 develop 分支代码到本地, 在 develop 分支的基础上创建新的 feature 分支.
  - 一旦某 feature 分支的代码开发完成后(须保证自己完成了基本的功能自测工作), 合并该分支到远程的 develop 分支中, 具体__合并操作流程查看 [0x04 节 Merge Request](2016-11-02-gitlab-gitflow-tutorial.html#merge-request) 的内容__.
  - 一旦某 feature 分支合并到 develop 分支后, __若所需开发的功能已完成__, 该 feature 分支的维护者须及时删除该分支(__包括本地与远程的分支__).

#### 对于已正式上线的版本, 当发现 bug 后需要进行修复时, 可分以下两种情况考虑:

1. ##### 该 bug 为非紧急问题, 可考虑在下一功能版本中再修复.  
  - 如新创建 feature 分支的流程一样, 以 develop 分支为基础创建 bugfix 分支, 比如: `bugfix/login-error`. 在该分支上完成修复工作后合并回 develop 分支, 并等待下一次的发包. __(合并操作查看 [0x04 节 Merge Request](2016-11-02-gitlab-gitflow-tutorial.html#merge-request) 的内容)__   
 
  - 其它正进行开发中的 feature 分支注意 pull 该次合并提交.

2. ##### 该 bug 为紧急问题, 须即刻修复.
  - 以 master 分支中上一次的发版本提交点为基础, 创建 bugfix 分支. 完成修复工作, 并通过测试后, 合并回 master 分支并进行重新发包.   
  - 其它的分支注意 pull 该次合并提交.


##  <span id="merge-request">0x04 Merge Request</span>
__为减少合并时冲突的发生, 以及防止因合并不当而引致他人工作的丢失, 合并操作通过在 gitlab 平台上发起 Merge Request 的方式完成.__

假设同学 A 在 特性分支 `feature/support-video` 上完成了支持视频播放的功能, 现在 TA 需要向gitlab 的 develop 分支进行代码合并, 合并的流程以下:

1. __首先在本地进行 `git fetch --all`, 先将远程库上的最新分支数据信息拉取到本地__

2. 现在可以保证本地的 develop 分支为最新的了, __将本地的 `develop` 分支合并到本地的 `feature/support-video`__ 分支上. (是的, 是将本地的 develop 合并到 feature/support-video 上. 这样可减少远程发起 merge request 时冲突的产生, 因为这时如果有冲突, 在这个操作之后, 本地会提示冲突, 你可以在本地解决它)

  ```bash
$ git checkout feature/support-video  # 确保已切换到要合并的分支上, 若之前已经在该分支可忽略
$ git merge develop  # 合并本地的 develop 到 feature/support-video
```

  在进行上面的操作后, 若一切都很顺利, 你可以直接跳到第 __5__ 步.
  
  但如果这时你很不幸, 出现了冲突(这种情况在多人协同工作时可能经常出现, 请保持好心情), 你需要先解决掉冲突的文件, 这时最好联系一下和你同时修改了相同文件地方的人.

3. __再次进行测试. 再次进行测试. 再次进行测试__. 觉得没问题后, 再进行下一步.

4. 提交.

  ``` bash
  $ git add `"冲突的文件"`
  $ git commit -m `"这个是提交信息"`
```

5. push `feature/support-video` 分支到 gitlab 平台上, 并到 gitlab 平台上创建新的 merge request. 项目管理者收到 merge request 时, 通过代码 review 后进行 merge 操作.  

    > 如果创建新的 merge request 时有冲突, 或者项目管理者在__接受合并操作__时提示有冲突, 联系 merge request 的发起人, 重走一遍该 merge request 流程即可)


## 0x05 Commit Message(提交说明)
每次 commit 操作须撰写相应的 commit message. 其应该清晰明了, 说明本次提交的目的.  
一表胜千言, 以下是一些 good 与 bad 的对比:

Good|Bad
:---|:---
add test unit about upload file | add test
add image resource about login UI | add resource
fix issue about login not working | fix bug
refactor code about network handle by using volley | refactor code

> 在提交说明中添加 `#issue_num`, 可将本次提交直接对应到该 issue.  
> `issue_num` 为整数, 在 gitlab 上发布 issue 时会得到


## 0x06 分支命名
全部的分支命名采用 __小写字母__, 并且不要使用中文字符. 在迫不得已的情况下, 尽量 __不要使用拼音__ 来命名. 对于没有实质意义的缩写, 与其出现在分支命名中, 不如删除它, 让分支名保持简洁.

除了默认的 master 分支与 develop 分支外, 新建的分支命名采用以下的规范:

- 新功能特性分支: feature/`功能名称`   
  比如: __feature/support-video__ __feature/browser-history__
  
- bug 修复分支: bugfix/`具体问题`  
  比如: __bugfix/login-prompt-error__ __bugfix/register-typo__

- 其它类型的分支: 采用 `英文名词/简单说明` 的形式.

> 简洁是美德.


## 0x07
> 没有0x07