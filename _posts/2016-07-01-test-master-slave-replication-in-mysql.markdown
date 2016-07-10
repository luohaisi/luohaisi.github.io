---
layout: post
title:  "mysql主从服务器配置过程"
date:   2016-07-01 11:10:18 +0800
author:     "Luo"
header-mask: 0.3
catalog:    true
tags:
    - Mysql
---

> 一直想自己自体验一下，配置mysql主从复制的过程，从网上的资料来看,操作比较简单，
> 但是实践出真知，操作过程中还是遇到了一些坑，所以就有了这篇日志。

## 运行环境

* 本地系统：win10 64位 mysql版本 5.6.17

* virtualbox上跑着两台虚拟机：centos7 mysql版本 5.5.47-MariaDB

| 系统          | mysql版本      | ip            | 备注   |
| ------------- |:--------------:| :------------:| ------:|
| win10 64位    | 5.6.17         | 199.10.10.164 |本地系统|
| centos7       | 5.6.17         | 199.10.10.155 |virtualbox虚拟机|
| centos7       | 5.5.47-MariaDB | 199.10.10.150 |virtualbox虚拟机|


## 操作步骤一

给所有mysql服务器开启binlog(二进制日志)

修改my.cnf文件: `#vi /etc/my.cnf`

```yml
[mysqld]
log-bin = mysql-bin
server-id=155      #默认是1，一般取IP最后一段
```

重启mysql服务

```sh
mysql restart
```

或者

```sh
service mariadb restart
```

## 操作步骤二

在主服务器上建立帐户并授权slave，  *用户名：yourslave，密码：123456*

```sql
GRANT REPLICATION SLAVE ON *.* to 'yourslave'@'%' identified by '123456';
```

登录主服务器的mysql，查询master的状态

```sql
mysql>show master status;
```

> File: mysql-bin.000002    Position: 268

**注：执行完此步骤后不要再操作主服务器MYSQL，防止主服务器状态值变化**

配置从服务器Slave

```sql
change master to 
master_host='199.10.10.155',
master_user='yourslave',
master_password='123456',
master_log_file='mysql-bin.000002',
master_log_pos=245;
```

启动从服务器

```sql
Mysql>start slave;
```

检查从服务器复制功能状态

```sql
mysql> show slave status \G;
```

**注：Slave_IO及Slave_SQL进程必须正常运行，即YES状态，否则都是错误的状态(如：其中一个NO均属错误)。**

## FAQ

> MYSQL的版本必须一致？

如上，我的mysql版本不一致，而且是mysql和mariadb两个大版本，一样可以进行配置，
可能是比较高的版本就不会存在兼容的问题，待考察

> mysql数据库内容必须为空？

不必要，我的一个数据库是有内容的，一样可以进行配置，
复制从Position的位置开始，之前的数据一概忽略不计。

> 需要注意的事项

配置过程中，尽量手敲键盘打字，避免 `ctrl+C`,`ctrl+V`,因为会产生意想不到的错误，可能是多复制了一个空格，注意用户名密码等重要信息不要打错，否则后面很难发现错误，很多未知错误都是由于打错了一个字符造成的...