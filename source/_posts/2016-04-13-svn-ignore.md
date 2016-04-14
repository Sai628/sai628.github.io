---
title: svn:ignore的用法(忽略文件及目录)
date: 2016-04-13 12:13:55
tags: [svn]
categories:
---


在svn中忽略文件或目录,可以通过svn:ignore命令来设置.  
具体可分为以下几种情况:
<!-- more -->


* __若想创建一个文件夹,并且把它加入版本控制,但忽略文件夹中的所有文件内容:__
	
	```bash
	$ svn mkdir myDir
	$ svn add myDir
	$ svn commit -m "add myDir"
	$ svn propset svn:ignore '*' myDir
	$ svn commit -m "ignore contents in myDir"
	```
	

* __若想创建一个文件夹,但不加入版本控制,即忽略这个文件夹:__

	```bash
	$ mkdir myDir
	$ svn propset svn:ignore 'myDir' .  #注意最后的`.`不能少
	$ svn commit -m 'ignore myDir'
	```
	
* __若已经创建了文件夹,并且加入了版本控制,现在想忽略这个文件夹,但要保持文件夹的内容:__

	```bash
	$ svn export myDir myDir-temp`  #将myDir导出为myDir-temp,并且导出后myDir-temp不受版本控制
	$ svn rm myDir
	$ svn commit -m 'remove myDir'
	$ mv myDir-temp myDir
	$ svn propset svn:ignore 'myDir' .  #注意最后的`.`不能少
	$ svn commit -m 'ignore myDir'
	```

* __若要忽略文件夹里的某几个文件:__

	```bash
	$ svn mkdir myDir
	$ svn add myDir
	$ svn commit -m 'add myDir'
	$ vi .svnignore  #创建/编辑.svnignore文件
	```
	将待忽略的文件名添加到`.svnignore`中,一行一个.例如:  
	.svnignore  
	\*.a  
	\*.out  
	test.txt  
		
	最后:  
	```bash
	$ svn propset svn:ignore -F .svnignore  #参数 -F 表示从某文件中读取属性值
	$ svn commit -m 'ignore some files'
	```


