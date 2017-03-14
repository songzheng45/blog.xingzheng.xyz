---
title: 'Could not load file or assembly System, Version=2.0.5.0 in .NET 4'
category: 'C#/.NET'
date: 2017-03-15 00:15:05
tags: [.NET Framework,Patch,补丁]
---

>背景：  
最近项目中有个小需求，使用C#开发一个客户端，然后打包成安装程序给客户使用。  
由于是 .NET 平台，自然要先安装 .NET Framework。据需求同事反馈，不排除有客户仍然使用 XP 的可能性，而 XP 不支持 .NET 4.5 及以上版本。因此选择 .NET Framework 4.0 作为目标框架，也能够满足开发需要。  
于是新建一个引导页面，引导客户分别安装客户端和 .NET Framework 4.0。  

## 出现问题
很快第一个版本测试后上线，不久就反馈回来一个奇怪的问题：`Could not load file or assembly System, Version=2.0.5.0`。  
客户环境是 Windows Server 2008，也已安装 .NET Framework 4.0，但是这个报错意思貌似是要去加载`System.dll`，但是找的却是2.0版本。纳闷儿了，应该去找4.0版本的才对啊！  

<!--more-->

## 解决问题
于是Google搜索，很快就在StackOverFlow上找到答案：“确保给.NET Framework 4.0打过补丁”。  
补丁修复的一个问题是，让便携式类库能够找到正确的运行时（[KB2468871][KB2468871_link]），于是安装相应的补丁包后顺利解决。

## 总结
基本可以确定，这个问题是由于客户端程序依赖的一个识别验证码的NuGet组件导致的，而我们开发时自己环境都是安装最新的 .NET Framework，所以给忽略掉了。  

由于大部分时间都在做Server端，Client端系统环境差异很大，没有测试周全。






[KB2468871_link]:http://support.microsoft.com/kb/2468871