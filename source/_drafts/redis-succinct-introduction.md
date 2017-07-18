---
title: Redis 简明教程
tags: Redis
category: 技术
---

## Redis简介
Redis 是一个开源, BSD 授权,高级key-value 内存数据库.

## 安装 Redis
### macOS 下安装 Redis
使用 homebrew 安装:
``` bash
brew install redis
```
立即启动服务, 并在登录时重新启动:
``` bash
brew services start redis
```
停止服务:
``` bash
brew services stop redis
```
或者,如果不想要后台服务的形式运行,可以手动启动:
``` bash
redis-server /usr/local/etc/redis.conf
```

验证 Redis 是否启动:
``` bash
$ redis-cli ping
PONG
```

## Redis 数据类型
[Data types][1]
### Strings

### Lists

### Sets

### Hashes

### Sorted sets

### 

## 常用命令和配置
### 自定义监听端口

### 使用配置文件

### 配置允许远程访问


## Redis 客户端
### StackExchange.Redis
`StackExchange.Redis` 是 .NET 平台下的高性能,通用的 Redis 客户端.

基本用法:



<!--more-->



## 遇到的问题
### Could not connect to Redis Connection refused
``` bash
$ redis-cli
Could not connect to Redis at 127.0.0.1:6379: Connection refused
```






[1]:https://redis.io/topics/data-types