---
title: Supervisor入门
tags:
  - supervisor
category: 技术
date: 2017-06-21 09:32:02
updated: 2017-06-21 09:32:02
---

## 关于 Supervisor
Supervisor 是一个用 Python 写的进程管理工具，可以很方便的用来启动、重启、关闭进程（不仅仅是 Python 进程）。除了对单个进程的控制，还可以同时启动、关闭多个进程，比如很不幸的服务器出问题导致所有应用程序都被杀死，此时可以用 supervisor 同时启动所有应用程序而不是一个一个地敲命令启动。

supervisor 是一个 C/S 模型的程序，由 server 和 client 模块组成：
1. supervisord
2.  supervisorctl
supervisorctl 是命令行工具，我们通过它可以在终端操作 supervisord ，例如启动、停止任务，详细的命令可使用`supervisorctl -h` 查看。

官方文档：http://supervisord.org/

<!--more-->

## 快速安装
1. macOS 下使用 Homebrew安装
```bash
$ brew install supervisor
```
2. Ubuntu 使用 apt 或 apt-get
```bash
$ apt install supervisor
```

3. pip安装
supervisor 是用 Python 写的，所以可以使用pip（据说不支持Python3，但是有Python3的移植版本）
```bash
$ pip install supervisor
```

## 配置
### 重定向配置文件
安装完 supervisor 之后，可以运行 `echo_supervisord_conf` 命令输出默认的配置项，一般会将配置项重定向到一个配置文件里。使用以下命令：
```bash
echo_supervisord_conf > /etc/supervisord.conf
```
如果提示 `Permission Denied`，则使用以下命令：
```
$ sudo su root -c "echo_supervisord_conf > /etc/supervisord.conf"
```

### 修改配置文件
对配置文件做以下改动，并取消注释[include]：
```ini
...
[unix_http_server]
file=/var/run/supervisor.sock   ; (the path to the socket file)
...

[supervisord]
logfile=/var/log/supervisor/supervisord.log ; (main log file;default $CWD/supervisord.log)
...

[supervisorctl]
serverurl=unix:///var/run/supervisor.sock ; use a unix:// URL  for a unix socket
...

[include]
files = /etc/supervisor/conf.d/*.ini
```
**注意**： `unix_http_server` 跟 `supervisorctl` 中的 sock 文件路径要一致 
`[include]`  项表示包含指定目录下所有`*.ini`  作为脚本，我们可以为每个任务创建一个ini文件，以方便管理。
如果这时目录 `conf.d` 还不存在，我们要先创建该目录：
```bash
$ sudo mkdir /etc/supervisor/conf.d
```
之后管理任务的脚本放到 `conf.d` 就行了。

### 编写任务脚本
netcorejob.ini
```
[program:netcorejob]
command=dotnet   NETCore.dll
directroy=/home/ubuntu/netcore/
autostart=true
autorestart=true
startretries=3
user=ubuntu
stdout_logfile=/log/supervisor/netcore.out.log
stderr_logfile=/log/supervisor/netcore.err.log
```

### 启动任务
```bash
$ sudo supervisorctl start netcorejob
netcorejob: started
```
### 停止任务
```bash
$ sudo supervisorctl stop netcorejob
netcorejob: stopped
```
### 查看任务状态
```bash
$ sudo supervisorctl
netcorejob                       RUNNING   pid 24330, uptime 0:04:37
```

### 重启和重载
[Supervisord: Restarting and Reloading][4]
- `service supervisor restart`
重启 supervisor 服务，它会停止、然后再启动所以管理的程序。  
使用该命令，不会应用修改后的配置。

- `supervisorctl restart <name>`
重启程序，且不会应用修改后的配置。

如果你创建了新的配置，以上命令都不会使它们可用。如果要在现有的和新的配置中应用更改、启动新的应用程序，并且重启所有管理的应用程序，你应该运行：
```
service supervisor stop
service supervisor start
```

如果你不想重启所有管理的程序，但是想使修改的配置生效，运行这个命令：
```
supervisorctl reread
```
`supervisorctl reread` 命令只是更新所做的修改，但不会重启所有管理的程序，即使它们的配置已经发生更改。


```
supervisorctl update
```
`update` 命令重启那些配置发生更改的程序。  
如果是新增的配置则这时变为可用，但不会自动启动，除非 supervisor 服务重启或系统重启（即使配置中 autostart=true）。

要启动新的应用程序，例如app2，使用以下命令：
```
supervisorctl start app2
```

## 问题
重启 supervisior 服务失败
```bash
$ sudo service supervisor restart
Failed to restart supervisor.service: Unit supervisor.service is masked.
```

## 用 Supervisor 运行 Shadowsocks
[用 Supervisor 运行 Shadowsocks][5]

## 参考
[ASP.NET Core Linux下为 dotnet 创建守护进程（必备知识）][1]
[ [supervisor] 使用小记(入门教程)][2]
[使用 supervisor 管理进程][3]



[1]:http://www.cnblogs.com/savorboard/p/dotnetcore-supervisor.html
[2]:http://blog.csdn.net/orangleliu/article/details/45057377
[3]:http://liyangliang.me/posts/2015/06/using-supervisor/
[4]:http://www.onurguzel.com/supervisord-restarting-and-reloading/
[5]:https://github.com/shadowsocks/shadowsocks/wiki/%E7%94%A8-Supervisor-%E8%BF%90%E8%A1%8C-Shadowsocks
