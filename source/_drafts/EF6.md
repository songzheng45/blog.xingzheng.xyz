---
title: Entity Framework Core笔记
tags:
    - Entity Framework
category: 技术
---

## 什么是 Entity Framework
>Entity Framework is an object-relational mapper (O/RM) that enables .NET developers to work with a database using .NET objects. It eliminates the need for most of the data-access code that developers usually need to write.  
>Entity Framework是一个对象-关系映射（O/RM）工具，允许 .NET 开发者使用 .NET 对象来操作数据库。它减少了开发者通常需要编写的访问数据库的大部分代码。  

EF现在有两个实现: EF6.x 和 EF Core，两者的区别看[这里](efcore-and-ef6)。

## EF Core with Sqlite
参考 [.NET Core - New database](https://docs.microsoft.com/en-us/ef/core/get-started/netcore/new-db-sqlite)

安装 `Microsoft.EntityFrameworkCore.Sqlite` 和 `Microsoft.EntityFrameworkCore.Design`   
``` bash 
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
```
编辑 `*.csproj`文件，手动添加 `DotNetCliToolReference` 为 `Microsoft.EntityFrameworkCore.Tools.DotNet`。  
添加完之后的 `csproj` 文件如下： 
``` bash
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <OutputType>Exe</OutputType>
    <TargetFramework>netcoreapp1.1</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Sqlite" Version="1.1.1" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="1.1.1" PrivateAssets="All" />
  </ItemGroup>
  <ItemGroup>
    <DotNetCliToolReference Include="Microsoft.EntityFrameworkCore.Tools.DotNet" Version="1.0.0" />
  </ItemGroup>
</Project>
```

## 根据Model创建数据库
创建Model后，使用 [migrations][introduction-to-migrations] 命令生成数据库： 
- 运行 `dotnet ef migrations add InitialCreate` 构建迁移，并为Model生成初始表。
- 运行 `dotnet ef database update` 以将新迁移应用于数据库。此命令在应用迁移之前创建数据库。

## 修改Model
- 运行 `dotnet ef migrations add` 命令构建一个新的迁移，以使相应的模式更改到数据库。检查脚手架代码（并进行任何必需的更改）后，可以使用 `dotnet ef database update` 命令将更改应用于数据库。
- EF使用数据库中的 `__EFMigrationsHistory` 表来跟踪哪些迁移已经应用于数据库。
- 由于SQLite的限制，SQLite不支持所有迁移（模式更改）。请参阅[SQLite Limitations][SQLite Limitations]。对于新开发，请考虑删除数据库并创建新数据库，而不是在模型更改时使用迁移。


 





[ef]: https://docs.microsoft.com/en-us/ef/
[ef6-doc]: https://msdn.microsoft.com/en-us/library/aa937723(v=vs.113).aspx
[efcore-and-ef6]: https://docs.microsoft.com/en-us/ef/efcore-and-ef6/index

[introduction-to-migrations]:https://docs.microsoft.com/zh-cn/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations
[SQLite Limitations]:https://docs.microsoft.com/en-us/ef/core/providers/sqlite/limitations