---
layout: post
title:  "PHP统一开发环境快速配置"
date:   2017-04-10 16:39:18 +0800
author:     "Luo"
header-mask: 0.3
catalog:    true
tags:
    - vagrant
---

> 使用vagrant配置统一开发环境的过程记录

### 安装软件

* [virtualBox v5.1.18](https://releases.hashicorp.com/vagrant/1.8.6/)
 (v5.1.16之前的版本会报错误:
```
Vagrant Error: Unable to Mount VirtualBox Shared Folders (Guest Additions, vboxsf)
```
见[stackoverflow](http://stackoverflow.com/questions/42074246/vagrant-error-unable-to-mount-virtualbox-shared-folders-guest-additions-vboxs))

* [vagrant v1.8.6](https://releases.hashicorp.com/vagrant/1.8.6/)(```目前的1.9.3版本也会报错```)

* [Laravel Homestead](https://laravel.com/docs/5.4/homestead)

安装 Laravel Homestead Vagrant Box

```
vagrant box add laravel/homestead
```

安装 Homestead

你可以把 Homestead 安装在常用文件夹下面，因为Homestead box将作为host应用在你所有的laravel项目中。
首先从github上clone仓库

```
git clone https://github.com/laravel/homestead.git Homestead
```

你可以在[Github](https://github.com/laravel/homestead/releases)上查找最新的稳定版本:

进入 Homestead

```
git checkout v4.0.5  // 当前版本已经是5.2.1了
```

生成 Homestead.yaml文件

Mac / Linux

```
bash init.sh
```

Windows

```
init.bat
```

> Laravel Homestead is an official, pre-packaged Vagrant box that provides you a wonderful development
> environment without requiring you to install PHP, a web server, and any other server software on your local
> machine. No more worrying about messing up your operating system! Vagrant boxes are completely disposable. If 
> something goes wrong, you can destroy and re-create the box in minutes!

### 配置文件

windows下Homestead.yaml位于：

```
C:\Users\Administrator\.homestead\Homestead.yaml
```

```
---
ip: "192.168.10.10"
memory: 2048
cpus: 1
provider: virtualbox

# authorize: ~/.ssh/id_rsa.pub

# keys:
#     - ~/.ssh/id_rsa

folders:
    # - map: ~/Code
    - map: D:/workspace/vagrant/code/loc
      to: /home/vagrant/Code/Laravel

sites:
    - map: homestead.app
      to: /home/vagrant/Code/Laravel/public

databases:
    - homestead

# blackfire:
#     - id: foo
#       token: bar
#       client-id: foo
#       client-token: bar

# ports:
#     - send: 50000
#       to: 5000
#     - send: 7777
#       to: 777
#       protocol: udp

```

### 修改Hosts文件

Mac/Linux, 位于 `/etc/hosts. `

Windows位于  `C:\Windows\System32\drivers\etc\hosts. `


```
192.168.10.10  homestead.app
```

### 安装laravel

Laravel 利用 Composer 管理它的依赖. 所以在使用 Laravel 之前, 确保本机上已经安装好了 Composer 。`homestead中以存在`

通过 Laravel 安装器

```
composer global require "laravel/installer"
laravel new newproject
```

或者通过 Composer Create-Project

```
composer create-project --prefer-dist laravel/laravel newproject
```



### 安装laravel库

新项目创建好后，一般关联的库没有完全下载到本地需要通过命令下载，这里有个坑，因为本地可能不具备开发环境，所以要进入虚拟机操作。

进入虚拟机环境

`vagrant ssh`

进入项目目录下，安装 ：

`composer install --no-script` 


### 创建Application Key

```
php artisan key:generate
```

如果.env文件不存在从.env.example复制

```
cp .env.example .env
```

未创建key页面会报错:
```
 The only supported ciphers are AES-128-CBC and AES-256-CBC with the correct
```

浏览器访问一下 `homestead.app`
 如果进入界面，恭喜你，安装成功了！

![](/statics/img/laravel.jpg)