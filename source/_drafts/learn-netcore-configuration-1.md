---
title: 学习 .NET Core 之配置（1）
tags: 
	- .NET Core
	- 配置
category: [技术]
---



说到传统 .NET 开发下的配置文件，自然就是 App.config 或 Web.config 文件了。那么到了全新的 .NET Core 平台下，配置文件有什么变化呢？



- 命令行配置
- Json 文件配置
- 从配置文件文本到 C#对象实例的映射 - Options 和 Bind
- 配置文件热更新
- 框架设计：Configuration

<!--more-->



## 命令行配置

示例代码如下：

```c#
using System;
using Microsoft.Extensions.Configuration;

namespace _01Configuration
{
    class Program
    {
        static void Main(string[] args)
        {
            var builder = new ConfigurationBuilder()
                .AddCommandLine(args);

            var config = builder.Build();   

            Console.WriteLine($"name:{config["name"]}");
            Console.WriteLine($"age:{config["age"]}");
        }
    }
}
```

以上示例是在程序入口处读取传入的参数，如： `dotnet program.dll name=robin age=28 ` 。



如果没有传入参数，可以设置默认参数值，代码如下：

```c#
class Program
{
    static void Main(string[] args)
    {
        var defaultArgs = new Dictionary<string, string>();
        defaultArgs.Add("name", "ROBIN");
        defaultArgs.Add("age", "18");

        var builder = new ConfigurationBuilder()
            .AddInMemoryCollection(defaultArgs)
            .AddCommandLine(args);

        var config = builder.Build();   

        Console.WriteLine($"name:{config["name"]}");
        Console.WriteLine($"age:{config["age"]}");
    }
}
```

那么执行程序时，如果没有传入参数，则读取默认参数。



## Json 配置

`config.json` 文件内容如下：

```json
{
  "Name": "朱元璋",
  "Position": "皇帝",
  "Age": "71",
  "job": [
    "造反",
    "当皇帝",
    "杀大臣"
  ]
}
```

注意得修改 json 文件的属性为"复制到输出目录"。

读取并输出 json 配置项的代码如下：

```c#
using System;
using Microsoft.Extensions.Configuration;

namespace _02JsonConfiguration
{
    class Program
    {
        static void Main(string[] args)
        {
            var builder = new ConfigurationBuilder()
                .AddJsonFile("config.json");

            var config = builder.Build();

            Console.WriteLine($"姓名: {config["Name"]}");
            Console.WriteLine($"职位: {config["Position"]}");
            Console.WriteLine($"年龄: {config["Age"]}");
            Console.WriteLine($"工作: {config["Job:0"]},{config["Job:1"]},{config["Job:2"]}");
        }
    }
}
```

