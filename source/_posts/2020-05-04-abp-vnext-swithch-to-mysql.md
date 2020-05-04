---
title: ABP vNext 切换数据库为 MySQL
tags:
  - ABP
  - MySQL
category: 技术
date: 2020-05-04 23:56:09
updated: 2020-05-04 23:56:09
---


基于启动模板创建的 ABP 项目，默认数据库是 SQLServer，如何切换到 MySQL 呢？下面我来总结一下。

<!--more-->

## 替换包 Volo.Abp.EntityFrameworkCore.SqlServer

将 `.EntityFrameworkCore` 引用的 `Volo.Abp.EntityFrameworkCore.SqlServer` 替换成 `Volo.Abp.EntityFrameworkCore.MySQL`

## 替换模块依赖 (Module Dependency)

编辑`.EntityFrameworkCore`项目里的 `xxxEntityFrameworkCoreModule.cs`类

- 将 `using Volo.Abp.EntityFrameworkCore.SqlServer;`替换为`using Volo.Abp.EntityFrameworkCore.MySQL;`
- 将 `DependenOn`里的`typeof(AbpEntityFrameworkCoreMySQLModule)`替换为`typeof(AbpEntityFrameworkCoreMySQLModule)`

## UseMySQL()

查找解决方案中所有的`UseSqlServer()`，并替换成`UseMySql()`.

## 修改数据库连接字符串

找到具有`appsetings.json`并需要连接数据库的项目，将数据库连接字符串修改为符合 MySQL 格式。 

可以参考 [connectionstrings.com](https://www.connectionstrings.com/mysql/) 。

> 通常需要修改`.DbMigrator`和`.Web`项目

## 修改 Migration DbContext

[IdentityServer module ](https://docs.abp.io/en/abp/latest/Modules/IdentityServer)有自己的数据库映射配置，默认使用 SQLServer 作为数据提供者，需要显式指定成 MySQL。

在项目`.EntityFrameworkCore.DbMigrations`里找到 `xxxMigrationsDbContext`, 修改如下：

```
builder.ConfigureIdentityServer(options =>
{
		options.DatabaseProvider = EfCoreDatabaseProvider.MySql;
});
```

## 重新生成迁移

1. 删除`.EntityFrameworkCore.DbMigrations`项目下的`Migrations`文件夹，并重新生成解决方案
2. 在命令行下切换到`.Web`项目目录，执行命令类似如下：

```
dotnet ef migrations add 'InitialCreate' 
					--project ../LearnAbp.BookStore.EntityFrameworkCore.DbMigrations  
```

3. 执行数据库迁移

```
dotnet ef database update 
				--project ../LearnAbp.BookStore.EntityFrameworkCore.DbMigrations
```

4. 运行`.DbMigrator`程序初始种子数据

## 启动程序

到此数据提供程序就完全切换成 MySQL ，可以启动`.Web`项目查看效果。