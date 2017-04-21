---
title: 第一个Cordova App
tags:
  - Cordova
category: 技术
date: 2017-04-21 23:51:33
updated: 2017-04-21 23:51:33
---


## Cordova 搭建开发环境

### 安装 Android SDK
可以在网上找到各种安装方式，不再赘述。我这里将Android SDK 安装在以下目录：
```
C:\Android\SDK
```
>注意：
>要将 Android SDK 所在目录添加到环境变量中，否则 Cordova 可能找不到 Android SDK。

### 安装 Cordova
因为 Cordova 命令行工具是作为一个 npm 包来分发的，因此需要预先安装 [Node.js](https://nodejs.org/en/download/) ，并且能够调用 **node** 和  **npm**命令 。  
假设已经安装好 Node.js 以及 npm，利用 npm 工具能自动下载 Cordova 模块。
- Windows 下：
```
C:\>npm install -g cordova
```
- on OS X and Linux:
```
$ sudo npm install -g cordova
```

### 创建App
切换到你的源代码目录，创建一个 Cordova 项目：
```
$ cordova create hello com.example.hello HelloWorld
```
这将会创建 Cordova app 需要的文件夹结构，默认情况， **cordova create** 脚本会生成一个基于Web应用的骨架，主页是项目下 *www/Index.html* 文件。

### 添加平台
以下所有命令都需要在项目目录下运行，或任意的子目录：
```
$ cd hello
```
添加你的app的目标平台，这里我们会添加 'ios' 和 'android' 平台，并且确保他们被保存到 **config.xml**：
```
$ cordova platform add ios --save
$ cordova platform add android --save
```
检查当前的平台集：
```
$ cordova platform ls
```
我的环境执行结果如下：
![](http://ojm289en8.bkt.clouddn.com/image/20170225/cordova-check-platforms.png)

### 检查必备的构建工具
```
> cordova requirements
```
可能会出现如下错误信息：  
![](http://ojm289en8.bkt.clouddn.com/image/20170225/Cordova-requirements-Android-SDK-failed.png)  
意思是没有找到 Android SDK。 如果没有安装在默认位置（一般在 C:\Program Files(x86)\Android\SDK），并且没有配置正确的环境变量，就会提示这样的错误。

打开环境变量窗口，根据提示在“用户变量”里添加一项变量，变量名为“ANDROID_HOME”，值为 Android SDK 安装目录：  
![](http://ojm289en8.bkt.clouddn.com/image/20170225/Android-SDK-Envrionment.png)  
点击“确定”保存，然后重启CMD，切换目录到 Cordova 项目，再次执行命令，可以看到不再有警告和错误提示：  
![](http://ojm289en8.bkt.clouddn.com/image/20170225/Cordova-Check-Android-SDK-Envrionment-Successfully.png)

**检查结果：**
>JDK：已安装
Android SDK：已安装
Anroid 目标平台： 已安装 android-19,....
Gradle：已安装 

### 编译App
任何初始化工作都要写在 *www/js/index.js*  的 *deviceready* 事件处理程序中。
运行以下命令将项目构建所有的平台：
```
$ cordova build
```
你可以限定构建的平台范围，这里以 -''android' 为例：
```
$ cordova build android
```

在构建过程中我遇到如下错误：
```
Unable to start the daemon process.
This problem might be caused by incorrect configuration of the daemon.
For example, an unrecognized jvm option is used.
Please refer to the user guide chapter on the daemon at http://gradle.org/docs/1.8/userguide/gradle_daemon.html
Please read below process output to find out more: 
---------------------------------------------------
Error occurred during initialization of VM
Could not reserve enough space for 2097152KB object heap
```

Google 后查到，需要在环境变量中添加一个变量：
`_JAVA_OPTIONS : -Xmx512M`

参考： [StackOverFlow](http://stackoverflow.com/questions/4401396/could-not-reserve-enough-space-for-object-heap)

再次编译：
```
BUILD SUCCESSFUL

Total time: 1 mins 45.002 secs
Built the following apk(s):
        D:/CordovaDemo/hello/platforms/android/build/outputs/apk/android-debug.apk
```
编译成功，并输出debug apk文件。

