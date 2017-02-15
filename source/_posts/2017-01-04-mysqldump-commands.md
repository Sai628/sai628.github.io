---
title: MySQL 数据备份命令 mysqldump 的使用
date: 2017-01-04 15:38:58
tags: [MySQL]
categories:

---


### 1. 将指定数据库备份到某dump文件中:

```bash
$ mysqldump -u[user-name] -p[password] [database-name] > [dump-filename]
```

Demo:

```bash
$ mysqldump -uroot -p123 test > test.dump
```

<!-- more -->
<br/>

### 2. --opt 参数

如果加上--opt参数则生成的dump文件中稍有不同：

  - 建表语句包含 `DROP TABLE IF EXISTS [table-name]`
  - `INSERT` 之前包含一个锁表语句 `LOCK TABLES [table-name] WRITE;`，`INSERT` 之后包含 `UNLOCK TABLES;`.

Demo:

```bash
$ mysqldump -uroot -p123 --opt test > test.dump
```

<br/>

### 3. 跨主机备份

```bash
$ mysqldump -u[user-name] -p[password] --host=[source-host] --opt [source-database-name] | mysql -u[user-name] -p[password] --host=[target-host] -C [target-database-name]
```

Demo:

```bash
$ mysqldump -uroot -p123 --host=12.12.34.34 --opt test1 | mysql -uroot -p456 --host=localhost -C test2
```

> 注意:  
> -C 参数用于指示主机间的数据传输使用数据压缩  
> 目标数据库 test2 需要提前创建  

<br/>

### 4. 只备份表结构 --no-data

```bash
$ mysqldump -u[user-name] -p[password] --no-data --databases [database-name1] [database-name2] [database-name3] > [dump-filename]
```

Demo:

```bash
$ mysqldump -uroot -p123 --no-data --databases test1 test2 test3 > test.dump
```

> --databases 参数用于指定主机上要备份的数据库名,  
> 如果要备份所有的数据库, 可以使用 _**--all-databases**_ 参数.

<br/>

### 5. 从备份文件恢复数据库

```bash
$ mysql -u[user-name] -p[password] [database-name] < [backup-filename]
```

Demo:

```bash
$ mysql -uroot -p123 test1 < backup.sql
```

<br/>

#### 一个完整的 Shell 脚本备份 MySQL 数据库示例:

```bash
$ vi /data/backup/backup.sh
```

```bash
#!/bin/bash

cd /data/backup
echo "You are in backup dir"

mv backup-* /data/old-backup/
echo "Old dbs backup are moved to old-backup folder"

File=backup-$(date +%Y%m%d_%H%M%S).sql
mysqldump -uroot -p123 test1 > $File

echo "Your database backup successfully completed!"
```

