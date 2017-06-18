---
title: Entity Framework Core笔记
tags:
    - EF
category: 技术
---

## 什么是 Entity Framework
>Entity Framework is an object-relational mapper (O/RM) that enables .NET developers to work with a database using .NET objects. It eliminates the need for most of the data-access code that developers usually need to write.  
>Entity Framework是一个对象-关系映射（O/RM）工具，允许 .NET 开发者使用 .NET 对象来和数据库交互，减少了通常需要开发者编写的大量访问数据库的代码。  

EF现在有两个实现: EF6.x 和 EF Core，两者的区别看[这里](efcore-and-ef6)。

这里只总结下 EF Core 的知识点。

<!--more-->

## EF Core with Sqlite
参考： [.NET Core - New database](https://docs.microsoft.com/en-us/ef/core/get-started/netcore/new-db-sqlite)


### 引用Nuget包和工具
安装 
`Microsoft.EntityFrameworkCore.Sqlite`   
`Microsoft.EntityFrameworkCore.Design`   
``` 
dotnet add package Microsoft.EntityFrameworkCore.Sqlite
dotnet add package Microsoft.EntityFrameworkCore.Design
```
编辑 `*.csproj`文件，手动添加 `DotNetCliToolReference` 为`Microsoft.EntityFrameworkCore.Tools.DotNet`。  
添加完之后的 `csproj` 文件如下： 
``` xml
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

### 根据Model创建数据库
创建Model类，然后使用 [migrations][introduction-to-migrations] 命令生成数据库： 
- 运行 `dotnet ef migrations add InitialCreate` 构建迁移，并为Model生成初始表。
- 运行 `dotnet ef database update` 将新的迁移应用到数据库。该命令在应用迁移之前创建数据库。

### 修改Model
- 运行 `dotnet ef migrations add` 命令构建一个新的迁移，以使相应的模式更改到数据库。检查脚手架代码（并进行任何必需的更改）后，可以使用 `dotnet ef database update` 命令将更改应用于数据库。
- EF使用数据库中的 `__EFMigrationsHistory` 表来跟踪哪些迁移已经应用于数据库。
- 由于SQLite的限制，SQLite不支持所有的迁移操作（如更改Model）。请参阅[SQLite Limitations][SQLite Limitations]。对于新开发的应用程序，请考虑删除数据库，再重新创建数据库，而不是在Model更改时使用迁移操作。

## 创建 Model
使用流式API在 `OnModelCreating`方法中配置 Model，一个好处的可以不用修改实体类。而且流式API具有最高优先级，会覆盖约定的和数据注解的配置。

### 包含（排除）类型
- 包含类型
以下类型会包含在 Model 中：   
在context的 `DbSet` 属性中公开的属性类型；    
在 `OnModelCreating` 方法中提及的类型；  
递归导航属性时找到的任意类型。

- 排除类型
1. 使用 `[NotMapped]` 数据注解；  
2. 使用流式API 
``` csharp
class MyContext : DbContext
{
    public DbSet<Blog> Blogs { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Ignore<BlogMetadata>();
    }
}

public class Blog
{
    public int BlogId { get; set; }
    public string Url { get; set; }

    public BlogMetadata Metadata { get; set; }
}

public class BlogMetadata
{
    public DateTime LoadedFromDatabase { get; set; }
}
```

### 包含（排除）属性
- 包含属性
根据约定，具有getter、setter方法的public属性会包括在 Model 中。

- 排除属性
1. 使用 `[NotMapped]`数据注解；
2. 使用流式API
```csharp
modelBuilder.Entity<Blog>()
            .Ignore(b => b.LoadedFromDatabase);
```
 
### 主键
1. 约定
 名为 `Id` 或 `<类型名称>Id` 的属性将被配置为实体的主键。
2. 使用数据注解`[Key]`
3. 使用流式API
```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Car>()
        .HasKey(c => c.LicensePlate);
}
```

组合主键只能通过调用流式API来配置，不能通过约定或数据注解的方式。
```csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasKey(c => new { c.State, c.LicensePlate });
    }
}

class Car
{
    public string State { get; set; }
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

### 生成值
用三种值的生成模式用于属性。

1. 不生成值
在将实体添加到context之前，必须给实体的每个属性提供有效的值




[ef]: https://docs.microsoft.com/en-us/ef/
[ef6-doc]: https://msdn.microsoft.com/en-us/library/aa937723(v=vs.113).aspx
[efcore-and-ef6]: https://docs.microsoft.com/en-us/ef/efcore-and-ef6/index

[introduction-to-migrations]:https://docs.microsoft.com/zh-cn/aspnet/core/data/ef-mvc/migrations#introduction-to-migrations
[SQLite Limitations]:https://docs.microsoft.com/en-us/ef/core/providers/sqlite/limitations