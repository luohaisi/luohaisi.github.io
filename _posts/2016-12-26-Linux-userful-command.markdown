---
layout: post
title:  "Linux 长尾命令集"
date:   2016-12-26 10:38:18 +0800
author:     "Luo"
header-mask: 0.3
catalog:    true
tags:
    - Linux
---

> 收集一些不太常用但很重要的命令

### 系统状态

查看系统发行版本信息

```sh
cat /etc/issue
```

查看内存使用情况

```sh
free -m
```

查看硬盘和分区

```sh
df -h
fdisk -l
```

### FTP管理

#### 建新用户
```sh
useradd –d /var/www -g ftp –s /sbin/nologin myftp
```

说明：

* -s /sbin/nologin 是让其不能登陆系统 

* -d 是指定用户目录为/var/www ，这里可以替换成任何你需要的目录。 

* -g ftp 把用户加入到ftp组中(vsftp已创建) 

* myftp是ftp用户名，在配置时把这个myftp换成你的ftp用户名。 

执行该命令后，如果出现这样的错误提示： 

```sh
useradd: warning: the home directory already exists. 
Not copying any file from skel directory into it. 
```
这说明用户的目录已经存在(/var/www，这个可以是任何你设定的目录)，不可再新建此目录，并非添加用户失败，可以忽略。

> 特别提醒：用useradd建立的用户，如果后面不加–s /sbin/nologin参数，建立的用户为普通系统用户，有系统登陆centos服务器的权限，在虚拟主机环境下，这非常危险。所以要在useradd命令后面加上参数–s /sbin/nologin，为centos 服务器虚拟主机添加不可以登录系统的ftp专用账号(虚拟账户)

#### 设置密码
```sh
passwd myftp 
```

> 提示:重复输入2次 

修改/var/www目录属性：（或任何自己定义的目录） 

```sh
chown -R myftp /var/www 递归把所有目录及文件的拥有者设为新添加的ftp用户 
chmod -R 755 /var/www 递归地设置拥护者全部权限,其他用户只有读取和执行权限 
```

这样,如果网站运行时需要有写入权限,可以直接通过ftp的用户名来修改。

#### 设置帐户只能访问自己的目录，

配置文件 /etc/vsftpd/vsftpd.conf

```sh
Chroot_local_user=yes
```


#### 删除用户

临时关闭：在/etc/shadow文件中属于该用户的行的第二个字段（密码）前面加上*就可以
了。想恢复该用户，去掉*即可。

或者使用如下命令关闭用户账号：

```sh
passwd peter –l
```

重新释放：

```sh
passwd peter –u
```

6、永久性删除用户账号

```sh
userdel peter
groupdel peter
usermod –G peter peter   （强制删除该用户的主目录和主目录下的所有文件和子目录）
```

#### 重启 vsftpd服务

```sh
service vsftpd restart
```

#### 查看所有用户及所有用户组 

```sh
groups // 查看当前登录用户的组内成员
groups username // 查看username用户所在的组,以及组内成员
whoami // 查看当前登录用户名

/etc/group // 文件包含所有组
/etc/shadow和/etc/passwd // 系统存在的所有用户名
```

#### 限制用户只能访问/指定，不能访问其他路径

> 此处似乎不是必须的

修改/etc/vsftpd/vsftpd.conf如下：  

```sh
chroot_list_enable=YES //限制访问自身目录
# (default follows)
chroot_list_file=/etc/vsftpd/vsftpd.chroot_list
```

编辑/etc/vsftpd/vsftpd.chroot_list文件，将受限制的用户添加进去，每个用户名一行
添加后如下：

```sh
test    /home/ftp
```

编辑 /etc/vsftpd/vsftpd.user_list文件，将受限制的用户添加进去，每个用户名一行
添加后如下：

```
    test
```

改完配置文件，不要忘记重启vsFTPd服务器

