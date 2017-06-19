---
title: 理解OWIN
tags: 
    - ASP.NET
    - Identity
category: 技术
---

## 什么是OWIN？
OWIN是Open Web Server Interface for .NET的首字母缩写，他的定义如下：
>OWIN在.NET Web Servers与Web Application之间定义了一套标准接口，OWIN的目标是用于解耦Web Server和Web Application。基于此标准，鼓励开发者开发简单、灵活的模块，从而推进.NET Web Development开源生态系统的发展。

OWIN是接口、契约，而非具体的代码实现，仅仅是规范([specifications(http://owin.org/spec/spec/owin-1.0.0.html)])，所以要实现自定义基于OWIN的Web Server必须要实现此规范。

## 为什么我们需要OWIN?
ASP.NET (System.Web)紧耦合IIS，IIS紧耦合 Windows，导致严重的局限性：  
- IIS和Windows捆绑，只能随Windows一起更新，而且无法跨平台
- System.Web更新慢，历史悠久，无法测试
- ASP.NET 复杂的生命周期成为累赘  

使用OWIN，Web Framework不再依赖IIS和OS，这意味着你能使用任何你想用的WebServer来替换IIS(比如：Katana或者Nowin)，并且在必要时随时升级，而不是更新操作系统。

<!--more-->

## OWIN的规范
### OWIN Layers
OWIN的规范非常简单，它定义了一系列的层（Layer），并且它们的顺序是以堆（Stack）的形式定义。如下图：  
![](http://images0.cnblogs.com/blog/299214/201505/312142198912477.png)  

OWIN定义了4层：  
- Host  
Host 是application和server在其中执行的进程，主要负责启动application。一些 Server 也是 Hosts。
- Server  
(The HTTP server that directly communicates with the client and then uses OWIN semantics to process requests.  Servers may require an adapter layer that converts to OWIN semantics.)  
HTTP server直接和客户端进行交流，然后用OWIN的语义去处理请求。 Server 可能需要一个适配器层（layer）来转换成 OWIN 的语义。









***
### 参考
[ASP.NET MVC随想录——漫谈OWIN](http://www.cnblogs.com/OceanEyes/p/thinking-in-asp-net-mvc-what-is-owin.html)  
[ASP.NET MVC随想录——锋利的KATANA](http://www.cnblogs.com/OceanEyes/p/thinking-in-asp-net-mvc-what-is-katana.html)  
[ASP.NET MVC随想录——创建自定义的Middleware中间件](http://www.cnblogs.com/OceanEyes/p/thinking-in-asp-net-mvc-create-custom-middleware.html)  
[ASP.NET MVC 随想录——开始使用ASP.NET Identity，初级篇](http://www.cnblogs.com/OceanEyes/p/thinking-in-asp-net-mvc-get-started-with-identity.html)
