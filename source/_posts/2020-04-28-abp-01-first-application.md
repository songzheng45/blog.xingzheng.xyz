---
title: 第一个 ABP vNext 程序（基于启动模板）
tags:
  - abp
category: 技术
date: 2020-04-28 14:25:23
updated: 2020-04-28 14:25:23
---


[ABP vNext](https://abp.io/) 是 ASP.NET Boilerplate 的下一代 Web 应用框架（详见博客[Introducing the ABP vNext](https://blog.abp.io/abp/Abp-vNext-Announcement)），它具有完整的架构和强大的基础设施用于快速开发现代化的 Web 应用。遵循最佳实践与惯例可以为你提供一个一致的（SOLID ）开发体验。
相关链接:

- [GitHub](https://github.com/abpframework/abp)
- 官方文档： [英](https://docs.abp.io/en/abp/latest) / [中](https://docs.abp.io/zh-Hans/abp/latest/)

新建一个 ABP 应用有两种方式：基于启动模板，或者从空项目开始。本文总结创建基于启动模板的解决方案，并说明每个项目的作用。

话不多说，我们马上开始创建第一个 ABP vNext 应用吧。

<!--more-->



## 安装 ABP CLI 命令

```powershell
dotnet tool install -g Volo.Abp.Cli
```

执行成功后就可以执行 `abp new` 命令创建指定模板的解决方案。

> macOS 下利用 zsh 安装后，可能会提示 `zsh: command not found: abp`，解决办法如下：
>
> 编辑`~/.zshrc`，将`~/.dotnet/tools`添加到PATH 中：
>
> `export PATH=$HOME/.dotnet/tools:$PATH`

## 解决方案结构

### 默认结构

如果不指定UI 框架，或未指定`--tiered`选项，则默认创建一个 ASP.NET Core MVC Razor Pages 项目：

```powershell
abp new LearnAbp.BookStore
```

等价于：

```shell
abp new LearnAbp.BookStore -u app
```



<img src="/images/image-20200427094447661.png" alt="image-20200427094447661" style="zoom:50%;" />

#### 项目依赖图

![](https://raw.githubusercontent.com/abpframework/abp/master/docs/en/images/layered-project-dependencies.png)

#### .Domain.Shared

共享层。

包含常量、枚举和其它对象，它们是领域层的一部分，但是需要被所有层（项目）使用。

该项目没有对其它任何项目的引用，但是被所有其它项目直接或间接引用。

#### .Domain

领域层。

主要包含实体（entities）、聚合根（aggregate roots）、领域服务（domain services）、值类型（value types）、仓库接口（repositories interfaces）和其它领域对象。

#### .Application.Contracts

应用契约（接口）层。

主要包含应用服务（application services）接口和 应用层的DTO（Data Transfer Object）类。

它存在作用是分离应用层的接口和实现，因此它能作为一个契约包分享给客户端。

例如： `IBookAppService`和`BookCreationDto`

它依赖`.Domain.Shared`，因为它可能使用常量、枚举和其它共享对象。

####  .Application

应用层。

包含定义在`.Application.Contracts`中接口的实现。

例如：`BookAppService`实现接口`IBookAppService`,并依赖`.Domain`项目来使用实体和领域对象来执行业务逻辑。

####  .EntityFrameworkCore

 EF Core 集成层。

定义`DbContext`并实现定义在`.Domain`中的仓库接口。

只有当你使用 EF Core 作为数据提供者的时候，该项目才有用。如果数据源用的是 MongoDB、Dapper 等等，该项目的名字和实现也会不同。

#### .EntityFrameworkCore.DbMigrations

EF Core 数据库迁移层。

它有一个单独的`DbContext`专门用于管理迁移。

ABP 是一个具有理想化设计的模块化框架，每个模块（module）有它自己的`DbContext`类。基于此，用于迁移的`DbContext`把所有`DbContext`配置整合到一个单独的 model 里来维护同一个数据库结构。

在高级场景中，我们可以有多个数据库和多个迁移`DbContext`类。

> 注意：
>
> 迁移`DbContext`仅用作数据库迁移，而不会在程序运行时调用。



依赖关系：

它依赖`.EntityFrameworkCore`，因为它重用应用程序里为`DbContext`定义的通用配置。

#### .DbMigrator

控制台程序，用来简化在开发、生产环境的数据库迁移。

- 必需时创建数据库
- 应用还未生效的迁移更改
- 需要时插入初始数据

特别地，初始种子数据在这里很重要。ABP 框架具有模块化的初始种子数据架构，详细可以[查看文档](https://docs.abp.io/en/abp/latest/Data-Seeding)。

该程序不仅可用于关系型数据库，也可用于为 NoSQL 数据库初始种子数据。

依赖关系：

- 依赖`.EntityFrameworkCore.DbMigrations`并访问定义其中的迁移。

- 依赖`.Application.Contracts`因此可以访问权限定义，默认授权 admin 所有权限。

#### .HttpApi

自定义 API Controller。

大多时候我们不需要定义API Controller，因为 ABP 框架 [Auto API Controller](https://docs.abp.io/en/abp/latest/API/Auto-API-Controllers)特性会基于应用程序分层自动创建它们。

依赖关系：

- 依赖`.Application.Contracts`以注入应用服务接口。

#### .HttpApi.Client

C#客户端代理，用来访问解决方案里的 HTTP API，并可以分享给第三方客户，为他们访问 HTTP API 提供方便。

由于[Dynamic C# API Clients](https://docs.abp.io/en/abp/latest/API/Dynamic-CSharp-API-Clients)，大多时候我们无需手动创建C#客户端代理。

> `.HttpApi.Client.ConsoleTestApp`项目演示如何使用这个客户端代理。

依赖关系：

- 依赖`.Application.Contracts`以共享远程服务使用的接口和 DTO 类。

>不需要的话可以删掉该项目。

####  .Web

该项目是 ASP.NET Core MVC UI 层，包含Razor Pages，JavaScript 文件，CSS 文件等等。

该项目包含主要的`appsettings.json`文件，包含数据库连接字符串以及其它程序配置项。

依赖关系：

- 依赖`.HttpApi`，因为UI 层需要使用 API和应用程序接口。

> 该项目引用了`.Application`和`.EntityFrameworkCore.DbMigrations`两个项目，而实际上这两个引用在 UI 层是不需要的，因为 UI 通常并不依赖于 EF Core 和 Application 层的实现。
>
> 这是由于创建解决方案时没有指定`--tiered`选项，因此`.Web`项目在单个服务里托管 UI，API和应用层。
>
> 总的来说，这种结构不符合 DDD 最佳实践，应该在展示层可以直接使用Domain Entity和仓储。

接下来看分层结构。

### 分层结构

```shell
abp new LearnAbp.BookStore --tiered
```

创建的解决方案如下图：

<img src="/images/image-20200427185518992.png" alt="image-20200427185518992" style="zoom:50%;" />

指定`--tiered`选项创建的是一个前后端分离的解决方案，并且多出两个项目`.IdentityServer`和`.HttpApi.Host`。

#### .IdentityServer

作为其它项目的认证服务。

`.Web`项目通过 *OpenId Connect Authentication* 从 IdentityServer 为当前用户获取 ID 和 access token，然后使用访问 token 去调用 HTTP API 服务。HTTP API 服务使用 bearer token 认证方式从 access token 中提取声明以授权当前用户。

![](https://raw.githubusercontent.com/abpframework/abp/master/docs/en/images/tiered-solution-applications.png)

ABP 使用开源的 [IdentityServer4](https://identityserver.io/) 框架在应用程序之间做认证，具体看[IdentityServer4 documentation](http://docs.identityserver.io/) 了解IdentityServer4 和 OpenID Connect 协议。

#### .HttpApi.Host

托管 HTTP API。

它有自己的`appsettings.json`来存放数据库连接和其它配置。

#### .Web

托管 UI，和默认结构里的一样。

不过这个`.Web`的`appsettings.json`不再需要连接数据库的配置，而是存放远程 API 和认证服务的地址。

#### 如何运行？

1. 启动 `.IdentityServer`，因为其他程序依赖它
2. 启动 `.HttpApi.Host` ，因为它被`.Web`调用
3. 启动 `.Web` 并登入系统

### Angular UI

如果选择 `Angular` 作为 UI 框架

```
abp new LearnAbp.BookStore -u angular
```

解决方案会被分成三个文件夹：

- `angular` 包含 Angular UI 程序，作为前端代码
- `aspnet-core` 包含 ASP.NET Core 解决方案，作为服务的代码
- `react-native` 包含 React Native UI 程序，作为移动端的前端代码

服务端代码和上面介绍的类似，不过不再有`.Web`项目。`*.HttpApi.Host`项目托管 HTTP API， `Angular`调用它。

项目结构详细说明，可参考官方文档： [Angular UI](https://docs.abp.io/en/abp/latest/Startup-Templates/Application#angular-ui)