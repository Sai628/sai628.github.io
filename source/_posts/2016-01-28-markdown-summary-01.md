---
title: Markdown语法概要(一)
date: 2016-01-28 17:50:21
tags: [Markdown]
categories:

---


## 字体
__1. 斜体 _italics_ __
在字符串的前后添加下划线 `_`.

``` bash
This is _italics_
```

This is _italics_ 

__2. 粗体 **bold** __
在字符串的前后添加两个星号 `**`.  

``` bash
This is **bold**
```

This is **bold**

当然,如果想斜体同时粗体的话, 可以像下面这样:

``` bash
This is **_italics and bold_***
```

This is **_italics and bold_**

<!-- more -->

## 字号
在某段字符串的最前面添加n个 `#` 号(n为1-6), 可以使该行变为 `header` 效果. 注意在 `#` 与文本之间添加一个空格.

``` bash
# This is header one
## This is header two
### This is header three
#### This is header four
##### This is header five
###### This is header six
```

效果如下:
# This is header one
## This is header two
### This is header three
#### This is header four
##### This is header five
###### This is header six


## 链接
`[]后加上()` []中为待添加链接的字符串, ()为链接对应的URl

``` bash
[google](https://www.google.com)
```
[google](https://www.google.com)


## 图片
图片的语法格式与链接非常类似, 唯一不同的是, 图片是在 `[]()` 的前面多加一个 `!`. 则 `![]()`

``` bash
![The first father](http://octodex.github.com/images/founding-father.jpg)
```
![The first father](http://octodex.github.com/images/founding-father.jpg)

另外, 也可以把 `()` 中的 URL 写在文档的最后, 用一个别名标记它. 型式如:

``` bash
[URL-1]: http://abc.com
```

然后, 在文档的其它地方, 只要引用了 `http://abc.com` URL 的地方, 就可以使用 `URL-1` 别名来替代. 需要注意的是, 此时的别名是用 `[]` 来包裹的, 而不是之前的`()`.

``` bash
![The second first father][Second Father]
```
	
需在文档的最后声明:

``` bash
[Second Father]: http://octodex.github.com/images/foundingfather_v2.png
```
	
![The second first father][Second Father]


## 块引用
块引用一般用于显示一段引用于别处的文本. 它的语法非常简单, 仅需要在某行文本的最前面添加一个 `>` 符号即可.

就像下面这样:

``` bash
> sure I can leave a light on. leave a light on for you.
```

效果如下:
> sure I can leave a light on. leave a light on for you.

注意: 当块引用的为包含空行的多行文本时, 需在每行的前面都添加上 `>`, 包括其中的空行. 这可以保证引用的文本块是整体的.

``` bash
> sure I can leave a light on. 
>
> leave a light on for you.
```

效果如下:
> sure I can leave a light on. 
>
> leave a light on for you.


[Second Father]: http://octodex.github.com/images/foundingfather_v2.png

