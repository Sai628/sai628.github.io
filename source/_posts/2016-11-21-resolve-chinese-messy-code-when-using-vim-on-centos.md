---
title: 解决在 CentOS 中使用 Vim 时中文乱码的问题
date: 2016-11-21 17:31:02
tags: [CentOS, linux, Vim, encoding]
categories:

---

在 Linux 开发中, 经常的会遇到乱码问题. 今天在 [阿里云](https://www.aliyun.com) 的一台 CentOS 服务器上, 使用 [Vim](http://www.vim.org) 查看一个 [Python](https://www.python.org) 源代码文件时, 其中的中文注释却出现了乱码. 一翻 [google](https://www.google.com) 之后, 此问题得到了解决.

打开文件: `/etc/vimrc`

``` bash
$ sudo vim /etc/vimrc
```

在文件中添加如下几行配置:

``` bash
set fileencodings=ucs-bom,utf-8,gbk,gb2312,cp936,gb18030,big5,latin-1
set encoding=utf-8
set termencoding=utf-8
set fileencoding=utf-8
```
<!-- more -->

## [Vim](http://www.vim.org) 中几个环境变量的解释

* ### termencoding
  终端使用的编码方式, 指定了键盘输出的编码格式和屏幕能够正常显示的编码方式.

* ### encoding
  设置了 Vim 内部缓冲区、寄存器、表达式等中存储的文本的编码方式. 它与文件的编码 `fileencoding` 可以是不一样的. 
  > 如果不一样, 中间会经过 [iconv](https://www.gnu.org/software/libiconv/) 转换.

* ### fileencoding
  文件自身的编码方式. 如果 `fileencoding` 与 `encoding` 不一致, 那么在读取和写入文件时, 会对编码方式进行转换.
  > 通过打开文件后设置 `fileencoding`, 我们可以将文件由一种编码转换为另一种编码.

* ### fileencodings
  这是一个字符编码的列表. 当 Vim 打开一个文件时, 会尝试使用列表中的第一个字符编码方式. 如果检测到错误, 就用列表中的下一个. 当找到一个可以正常工作的字符编码方式后, `fileencoding` 就被设置成找到的字符编码方式. 如果最后都失败了, `fileencoding` 就被设置空字符串, 这意味着字符的编码方式就跟 `encoding` 变量的值一样了.
  > 这样通过这个列表, Vim 可以自动判断文件的编码, 自动判断失败时还可手动设定 `fileencoding` 来指定编码. 因此, 设置 `fileencodings` 的时候, 一定要把要求严格的、当文件不是这个编码的时候更容易出现解码失败的编码方式放在前面, 把宽松的编码方式放在后面.


当 Vim 中遇到乱码时, 基本都是由于 Vim 没有能够正确地识别出文件的编码导致的.  
首先保证终端里可以正常显示中文, 然后使用命令 `:set fileencoding=GBK` 来设置 Vim 中此文件的正确编码. 可以使用命令 `:set fileencoding?` 查看 Vim 识别出的文件编码.
  