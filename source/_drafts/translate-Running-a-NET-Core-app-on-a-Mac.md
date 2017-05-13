---
title: 在 Mac 上运行 .NET Core 应用程序
tags: 
    - .NET Core
category: 技术
---

原文： http://michaelcrump.net/part3-aspnetcore/
***

## 简介
该系列文章的完整列表如下：
- [第一天 - 在 Windows 下安装和运行 .NET Core](http://www.cnblogs.com/songxingzheng/p/translation-getting-started-with-aspnetcore.html)
- [第二天 - Visual Studio 中的 .NET Core 模版一览](http://www.cnblogs.com/songxingzheng/p/translation-Taking-a-Look-at-the-Visual-Studio-Templates-for-NET-Core.html)
- [第三天 - 在 Mac 上运行 .NET Core 应用程序](http://www.cnblogs.com/songxingzheng/p/translate-Running-a-NET-Core-app-on-a-Mac.html)
- 第四天 - 从 .NET Core 应用程序创建 NuGet 包
- 第五天 - 创建一个 .NET Core 测试项目
- 第六天 - 将一个已有的 .NET Core 项目迁移到 csproj
- 第七天 - 创建一个 ASP.NET Core 应用程序
- 第八天 - 使用 Visual Studio Code 开发一个 .NET Core 控制台应用程序  

在这篇文章中，我们将观察下在命令行中运行 .NET Core App，接着在Mac上运行。

## 在 Windows 命令提示符中运行 App
虽然在Visual Studio中通过F5命令可以很轻松地运行应用程序，但是你也要知道可以在控制台中运行它。在开始之前，确保在[这里](https://github.com/mbcrump/DotNetCorePlayground)获取到应用程序。在下载应用程序后，在命令提示窗口中打开包含项目的文件夹。

你可以输入以下简单的命令来运行应用程序：  
```c#
dotnet run
```
可以得到以下输出：
```bash
C:\Users\mbcrump\Documents\visual studio 2015\Projects\NetCoreConsoleApp\src\NetCoreConsoleApp>dotnet run
Project NetCoreConsoleApp (.NETCoreApp,Version=v1.0) was previously compiled. Skipping compilation.
{
  "Active": true,
  "CreatedDate": "2017-02-20T00:00:00Z",
  "Email": "michael@blah.com",
  "Roles": [
    "User",
    "Admin"
  ]
}
```
在Visual Studio中运行可以得到同样的结果：  
![](http://michaelcrump.net/files/consoleapprunning1.png)  

## 使用 dotnet publish 发布到Mac
继续在命令提示符中输入`dotnet publish`，然后输入`tree`查看列出的目录，如下所示：  
```bash
C:.
├───bin
│   └───Debug
│       └───netcoreapp1.0
│           └───publish
├───obj
│   └───Debug
│       └───netcoreapp1.0
└───Properties
```
切换到到输出目录然后列出目录的文件：
```bash
02/08/2017  06:50 PM    <DIR>          .
02/08/2017  06:50 PM    <DIR>          ..
02/08/2017  06:50 PM             1,417 NetCoreConsoleApp.deps.json
02/08/2017  02:52 PM             6,144 NetCoreConsoleApp.dll
02/08/2017  02:52 PM            13,824 NetCoreConsoleApp.pdb
02/08/2017  06:50 PM               125 NetCoreConsoleApp.runtimeconfig.json
06/13/2016  10:06 PM           468,480 Newtonsoft.Json.dll
06/11/2016  10:14 PM            29,632 System.Runtime.Serialization.Primitives.dll
               6 File(s)        519,622 bytes
               2 Dir(s)   74,699,058,176 bytes free
```
需要注意，以下列出的引用的dll文件是我们在[上一篇博客](http://www.cnblogs.com/songxingzheng/p/translation-Taking-a-Look-at-the-Visual-Studio-Templates-for-NET-Core.html)中添加的：
- Newtonsoft.Json.dll
- System.Runtime.Serialization.Primitives.dll
只有 NetCoreConsoleApp.dll 是我们要在Mac上运行的控制台应用程序（或者其他支持.NET Core 的平台）。

## 在Mac上运行应用程序
终于是时候了！我猜你可能会这么说，我也同意。在Mac上运行这个app之前，你需要回到在[.NET Core下载页面](https://www.microsoft.com/net/core#macos)，安装 OpenSSL 和 SDK（或者运行时），如果你还记得[第一篇博客](http://www.cnblogs.com/songxingzheng/p/translation-getting-started-with-aspnetcore.html)上讲的它们的区别。

为了要在Mac上运行这个app，你需要将`publish`文件夹复制到你的Mac上。然后打开终端，只需要输入以下命令来运行这个app：
```c#
dotnet NetCoreConsoleApp.dll
```
![](http://michaelcrump.net/files/consoleappinmac.png)

太棒了！现在你有了一个运行在其他平台上的.NET Core 应用程序，你可以用之前就拥有的.NET技能来创建它。我**大爱** .NET Core！

## 结束语
好了，我要告一段落了，然后在下周回来。和以前一样，感谢你阅读这篇文章，如果觉得有用请点击分享按钮。此外，请随时在下面给我留言，或者在Twitter上关注我。