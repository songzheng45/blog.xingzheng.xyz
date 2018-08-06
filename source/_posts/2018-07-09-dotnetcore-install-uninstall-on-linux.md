---
title: Ubuntu 下安装、卸载 .NET Core
tags:
  - .NET Core
  - Ubuntu
category: 技术
date: 2018-07-09 18:48:41
updated: 2018-07-09 18:48:41
---


.NET Core [Get Started with .NET Core][3]

最新版本的 SDK 和 Runtime 前往[.NET Core下载页面][1]


## 仅安装Runtime
```bash
sudo apt-get install dotnet-sharedframework-microsoft.netcore.app-<version>
```
查看可用版本：
```
$ apt search dotnet-sharedframework-microsoft.netcore.app

```
如安装 Runtime 1.1.2：
```bash
sudo apt-get install dotnet-sharedframework-microsoft.netcore.app-1.1.2
```


## 卸载
1. 使用sh脚本卸载  
[点击这里][2] 下载卸载脚本，在 Ubuntu 下运行即可。


[1]:https://www.microsoft.com/net/download/core#/runtime
[2]:https://github.com/dotnet/cli/blob/rel/1.0.0/scripts/obtain/uninstall/dotnet-uninstall-debian-packages.sh
[3]:https://www.microsoft.com/net/core#linuxubuntu