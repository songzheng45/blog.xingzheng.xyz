---
title: 'C# 异步编程总结(APM)'
tags: 
    - C#
    - 异步
    - BeginInvoke
    - EndInvoke
category: [技术]
---

所谓异步, 就是在不阻塞主线程的情况下, 执行一些耗时的操作. 
某些耗时操作, 如请求网络,读取磁盘文件等, 无法预估执行时间, 如果执行时间太久, 会阻塞主线程并长时间等待.这些耗时操作, 就可以用异步的方式执行.

C# 中实现异步的方式目前有:
1. 委托和方法的 BeginInvoke/EndInvoke 
2. Async 和 Await 关键字

本篇文章主要介绍第一种方式, 即利用委托实现异步.

<!--more-->

使用委托实现异步的方式, 可以以异步方式调用任意的同步方法:
1. 定义委托
2. 定义并初始化一个委托变量
3. 定义回调方法, 在回调方法中调用 EndInvoke 方法获取返回值
4. 调用委托变量的 BeginInvoke 方法

代码示例:
```csharp
using System;
using System.Threading;
using System.Runtime.Remoting.Messaging;

/**
 * 本示例演示利用委托实现异步执行代码.
 */

// 定义委托
public delegate int AddDelegate(int x, int y);

public int Add(int x, int y)
{
     Console.WriteLine($" Add execting : IsThreadPoolThread:{Thread.CurrentThread.IsThreadPoolThread}");
    return x+y;
}

// 异步调用的方法执行完毕时自动进行调用的方法
public void OnAddComplete(IAsyncResult result)
{
   
    AsyncResult r = result as AsyncResult;
    AddDelegate del = r.AsyncDelegate as AddDelegate;
    int returnValue = del.EndInvoke(result);
    Console.WriteLine($"result = {returnValue}");
    Console.WriteLine($"pass value = {r.AsyncState}");
}

// 初始化委托变量
AddDelegate caller = Add;

// 给 BeginInvoke 方法传递的值, 在回调函数中, 通过 AsyncResult.AsyncState 获取到
string data = "Here I am!";

// 调用委托的 BeginInvoke 值
caller.BeginInvoke(4,9, OnAddComplete, data);

// 主线程模拟耗时5秒,等待异步操作执行完毕
Thread.Sleep(5000);
Console.WriteLine("Main Thread ends");

```

## 关于 Invoke/BeginInvoke 和 EndInvoke
Invoke/BeginInvoke/EndInvoke 是 CLR 自动为每个委托类型提供的方法, 因此委托才能够进行异步调用. 

