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


### 生成发行版的应用 Generating the release APK

```
$ cd android && ./gradlew assembleRelease

```

以上命令若执行失败,且报一下错误,
![](/statics/img/gradlew.png)

再次执行 `./gradlew assembleRelease --stacktrace` 试试,经常要执行两次才能打包成功!


### 开发环境需要安装 Python2 ? 

我本地安装的是 Python3 未发现异常.