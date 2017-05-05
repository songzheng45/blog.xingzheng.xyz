---
title: 'Could not load file or assembly System, Version=2.0.5.0 in .NET 4'
category: '技术'
date: 2017-03-15 00:15:05
updated: 2017-05-05 20:00:00
tags: [.NET Framework,Patch,补丁]
---

>背景：  
最近项目中有个小需求，使用C#开发一个客户端，然后打包成安装程序给客户使用。  
根据客户需求（可能还有人在使用XP），选择 .NET Framework 4.0 作为目标框架。  

## 问题描述
很快第一个版本测试后上线，不久就反馈回来一个奇怪的问题：`Could not load file or assembly System, Version=2.0.5.0`。  
客户环境是 Windows Server 2008，也已安装 .NET Framework 4.0，但是这个报错意思貌似是要去加载`System.dll`，结果找的却是2.0版本。应该去找4.0版本的才对啊！  

<!--more-->

## 分析问题
首先想到的是GAC的查找方式，但是没有头绪。

## 解决问题
通过Google搜索，很快就在StackOverFlow上找到答案：“确保给.NET Framework 4.0打过补丁”。  
补丁修复的一个问题是，让便携式类库能够找到正确的运行时（[KB2468871][KB2468871_link]），于是安装相应的补丁包后顺利解决。

## 总结
这个问题猜测是由于客户端程序依赖的一个识别验证码的NuGet组件引起的，而我们的开发环境安装的都是最新的 .NET Framework，所以测试时没有覆盖到这个情况。  






[KB2468871_link]:http://support.microsoft.com/kb/2468871