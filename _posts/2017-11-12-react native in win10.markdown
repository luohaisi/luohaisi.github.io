---
layout: post
title:  "React Native 初学者踩坑记 (win10)"
date:   2017-11-12 16:50:18 +0800
author:     "Luo"
header-mask: 0.3
catalog:    true
tags:
    - react-native RN
---

> RN版本0.49。

### 创建项目

使用`create-react-native-app`命令:
默认下载0.49版本的RN
须在expoapp下调试
默认没有创建android/ios文件夹
无需存在Android环境,创建项目后,直接`npm start` 运行
没有找到打包APK的办法

而使用`react-native init`命令创建的新项目:
默认下载的是0.50版本
无法使用expo实时调试
默认创建android/ios文件夹
需要存在Android环境,且环境变量都配置完整

### 开发环境 `Android Studio`

建议使用科学上网从官网直接下载最新版 [Android Studio](https://developer.android.com/studio/index.html)
根据提示进行安装,这样可以一次性的把所需安装的JDK 和模拟器一次性的下载完成.
但是,还不完整!
例如 `adb devices` 命令用于检查你的android设备或模拟器是否已经连接电脑,但是`adb`是需要你自行放入环境变量中,
默认目录 C:\Users\[用户名]\AppData\Local\Android\Sdk\platform-tools

同样的问题还有 `keytool` 用户生成签名 默认目录 C:\Program Files\Android\Android Studio\jre\bin

RN 需要安装Android SDK platform 23 版 所以按照教程勾选所需安装包
![](/statics/img/sdk23.png)
![](/statics/img/buildTools23.png)


### 生成签名 Generating Signed APK

生成签名按照[官网教程](http://facebook.github.io/react-native/docs/signed-apk-android.html)一步步走
遇到的坑是
`~/.gradle/gradle.properties` 文件找不到
原因是这是MAC的默认路径,win10不是存的这个目录


### 生成发行版的应用 Generating the release APK

```
$ cd android && ./gradlew assembleRelease

```

以上命令若执行失败,且报一下错误,
![](/statics/img/gradlew.png)

再次执行 `./gradlew assembleRelease --stacktrace` 试试,经常要执行两次才能打包成功!


### 开发环境需要安装 Python2 ?

我本地安装的是 Python3 未发现异常.

### 尝试安装图标插件 Victory Native 删除库之后遇到的问题

安装 Victory Native库,同时需要安装依赖 `npm install react-native-svg --save`,
并建立链接 `react-native link react-native-svg`,此处在删除库之后出现了大坑

卸载此列库之前,先要取消链接`react-native unlink react-native-svg` 否则在打包时总是报错,找不到配置之类的,很坑...
![](/statics/img/error1.png)
总结:Victory Nativ的卸载顺序

```
react-native unlink react-native-svg
npm uninstall react-native-svg --save
npm uninstall victory-native --save
```

### Xcode的archive菜单选项是灰色的

![](/statics/img/archive_xcode.png)

如果当前Xcode选择的设备是模拟器，那么"Archive"是灰色的，不能点。
把当前设备设置成连接的真机设备或者默认的"iOS Device"，"Archive"会变成黑色。

### 证书签名

在Xcode7之前，真机调试和打包ipa是必须要签名的。但是Xcode7出来了，真机调试不需要签名了。打包ipa也可以不需要签名，那不是可以绕过苹果安装app了？？想得美，如果不签名打包的ipa那是不能安装的！！！打包可以不需要签名，但是你要想安装的话，必须签名。

签名打包就是在编译的过程中加入证书签名，那肯定是需要去Xcode中配置的，目前网上有方法能实现企业级证书打包签名不需要在编译时，而在编译之后。因此现在打包有两种方式：
1、在Xcode中配置code sign，实现签名打包ipa。适用于企业级账号证书，个人账号证书。
2、在Xcode中无证书打包ipa，上传到指定工作室网站，淘宝上现在一大堆打包服务的，由别人用他们的账号签名ipa。适用于企业及账号。

### 证书、签名、私钥、描述文件的一句话描述

（1）   证书分两种：开发者证书、发布者证书。前者开发时使用，后者发布使用
（2）   模拟器调试无需代码签名；真机调试需开发者证书代码签名；发布时需发布证书签名
（3）   代码签名需要：证书+私钥，缺一不可
（4）   真机调试时要求在设备上安装描述文件（provision profile），该文件包含信息：调试者证书，授权调试设备清单，应用ID。一个应用对应一个描述文件。

### 一些教程

iOS Provisioning Profile(Certificate)与Code Signing详解
http://blog.csdn.net/phunxm/article/details/42685597

OS X想要访问您的钥匙串的几个解决方案
http://www.jianshu.com/p/a671f772e78e

iOS App上架流程(2016详细版）
http://www.jianshu.com/p/b1b77d804254

iOS APP 上架审核过程中常见问题整理
http://www.jianshu.com/p/a992c88087a5

IOS证书／私钥／代码签名／描述文件
http://blog.csdn.net/lvxiangan/article/details/17306269