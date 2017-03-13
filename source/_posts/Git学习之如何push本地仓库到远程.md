---
title: Git学习之如何push本地仓库到远程
date: 2017-03-12 20:07:02
tags: Git
---

演示：  
将一个本地仓库和一个远程仓库关联，并将本地仓库的修改提交到远程仓库。

>运行环境：
>操作系统： Windows
>远程仓库：Github
>Git   终端： Git Bash

## 1. 初始化本地仓库
打开 `Git Bash` ，切换到一个空目录：
``` bash
$ git init
$ git add .  
$ git commit -m '初始化'
```
这三行命令的作用分别是：  
1. 初始化git仓库
2. 将所有文件提交到 stage（暂存）
3. 将 stage 中的更改提交到本地仓库

<!-- more -->

## 2. 关联远程仓库
``` bash
# 关联远程仓库
$ git remote add origin <远程仓库地址>

# 验证远程地址
$ git remote -v
```
说明：  
这里的`<远程仓库地址>`就是你想推送的地址，而且有两种推送方式：`HTTPS`和`SSH`。   
两者的比较：
- `HTTPS`：
使用加密的网页访问通道读写仓库，使用用户名和密码进行鉴权。 避免重复输入密码，查看[怎样在每次 Push 时不用重复输入密码 ](#)
- `SSH`：    
有关什么是 SSH，可参考[中文维基百科](http://zh.wikipedia.org/zh/Secure_Shell)。  
使用加密通道读写仓库，无单次上传限制，需先设置“[账户 SSH 公钥](#账户 SSH 公钥)”，完成配对验证。



## 3. 推送到 GitHub
```bash
$ git push origin master
```
把本地仓库的更改推送到您指定为源（origin）的远程仓库。

***

附录：
## 怎样在每次 Push 时不用重复输入密码
有两种方法：

使用 SSH 方式进行推送，您需要配置 SSH 公钥后进行操作，详情请阅读 SSH 公钥配置文档。  

对于 Https 协议: 首先在全局配置保存你的密码， ~/.git-credentials （没有就创建）内添加 ` https://{username}:{passwd}@git.coding.net `。  
然后执行配置 Git 命令存储认证命令： `$git config --global credential.helper store`。  

执行后在 `~/.gitconfig` 文件会多出下面配置项: `credential.helper = store`

详情请参考凭证存储 。

## 账户 SSH 公钥
>账户 SSH 公钥是跟用户账户关联的公钥，一旦设置，SSH 就拥有账户下所有项目仓库的读写权限。 设置“账户 SSH 公钥”是开发者使用 SSH 方式访问/修改代码仓库的“前置工作”，分为“获取 SSH 协议地址”、“生成公钥”、“在 Github 添加公钥”三个步骤。  

1. 获取 SSH 协议地址
在 Github 仓库主页点击`Clone or download`下拉框，选择`Use SSH`，获得 ssh clone 地址，形如：`git@github.com:yourname/repositoryname.git`。

2. 生成公钥
打开 `Git Bash`，输入`ssh-keygen -t rsa -C "username@example.com"`，回车即可（也可以输入密码）：  
```bash
$ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
# Creates a new ssh key, using the provided email as a label
# Generating public/private rsa key pair.
Enter file in which to save the key (/Users/you/.ssh/id_rsa): [Press enter]  // 推荐使用默认地址,如果使用非默认地址可能需要配置 .ssh/config
```
成功之后：
```bash
$ ssh-keygen -t rsa -C "user_email@example.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/username/.ssh/id_rsa):
Created directory '/c/Users/think2/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/username/.ssh/id_rsa.
Your public key has been saved in /c/Users/username/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:gGKkPJBzB+7kDe9tyG+7gFDjJP8PrWgjv2YUmYQJwmc user_email@example.com
The key's randomart image is:
+---[RSA 2048]----+
|o....            |
|o+oEoo           |
|.o=+==o          |
| ..+Bo+o         |
|  ..**. S        |
|   ..+= .        |
|    o+o= .       |
|   . *=++        |
|    ++*=o.       |
+----[SHA256]-----+
```