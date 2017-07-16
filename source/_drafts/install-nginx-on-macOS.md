---
title: 在macOS下安装 Nginx
tags: 
    -Nginx
category: 技术
---

## 安装
最简单的方式莫过于`HomeBrew`安装.

执行安装命令:
```bash
brew install nginx
```
安装完之后, 启动 Nginx:
```
sudo nginx
```
打开 URL `http://localhost:8080`, 如果出现 Nginx 的欢迎页面, 则表示安装成功.

<!--more-->

## 配置

## 遇到的问题

### brew link 失败的解决办法
如果安装 HomeBrew 时不是用`sudo`身份安装的, 那么在安装 Nginx 过程中会有一个 ERROR:
```bash
Error: The `brew link` step did not complete successfully
The formula built, but is not symlinked into /usr/local
Could not symlink share/man/man8/nginx.8
/usr/local/share/man/man8 is not writable.

You can try again using:
  brew link nginx
```
大概意思是执行`brew link`链接命令失败, 导致的结果就是, 在终端里无法使用` nginx` 命令, 会提示:  
```
$ nginx
-bash: nginx: command not found
```
Error 中给出建议执行`brew link nginx`, 结果执行后继续报错:  
```
$ brew link nginx
Linking /usr/local/Cellar/nginx/1.12.1... 
Error: Could not symlink share/man/man8/nginx.8
/usr/local/share/man/man8 is not writable.
```
这就是brew 命令没有root权限导致的. 如果安装 HomeBrew 时没有使用 sudo, 那么今后执行需要 root 权限的命令时, 都会失败.   
解决办法是, 执行命令：sudo chown -R $USER /usr/local.   
其中 `$USER`是当前用户名, 将`/usr/local`文件夹的所有者变更为当前用户.  
如：`sudo chown -R chenjunbiao /usr/local`.   
然后再执行`brew link nginx`, 就可以在终端使用`nginx`命令了.   




