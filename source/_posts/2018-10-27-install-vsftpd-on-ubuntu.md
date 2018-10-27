---
title: install-vsftpd-on-ubuntu
date: 2018-10-27 20:14:06
updated: 2018-10-27 20:14:06
tags:
    - ftp
    - Ubuntu
category: 技术
---

## 安装
```
sudo apt-get install vsftpd
```
安装程序会给本地创建一个名为“ftp”的用户组，命令执行完之后会自动启动FTP服务。

## 检查FTP服务是否启动
```
netstat -ntl
```
查看是否打开21端口，输出：
```
$ sudo netstat -npltu | grep 21
tcp        0      0 0.0.0.0:21              0.0.0.0:*               LISTEN      15601/vsftpd   
```

## 开启、停止、重启vsftpd服务的命令：
```
service vsftpd start | stop | restart 
```

<!--more-->

## 准备FTP登录用户
```
useradd vsftpd -s /sbin/nologin -m ftpuser
```
- ftpuser：将要创建的ftp用户名
- -s：指定shell，参数是：/sbin/nologin，意思是创建的ftpuser无法从系统登录
- -m：创建该用户的家目录（/home/ftpuser）

## 配置vsftpd
```
sudo nano /etc/vsftpd.conf
```
主要做以下修改：
>anonymous_enable=NO
local_enable=YES
write_enable=YES
local_root=/var/www     （/var/www 是ftp用户登录后进入的根目录，不能再切换到其他目录）

## 修改目录权限（如果没有就创建）
```
mkdir /var/www
chmod 777 /var/www
```

## 重启vsftpd
```
sudo service vsftpd restart
```

## 卸载
```
sudo apt-get remove --purge vsftpd
```

### xftp连接vsftpd的一些问题

1. 传文件的根目录是“/var/www”，使用本地用户sxz登录到ftp， 提示：“553 could not create file”
**原因**：用户sxz对/var/www没有权限。
可以看到，/var/www 文件夹的所有者和群组是 `root:root`，因此用户sxz没有权限。
执行命令：  `sxz@ubuntuserver2:~$ sudo chown -R sxz:sxz /var/www`     
将目录“/var/www”所有者和群组修改为用户sxz。
重试上传，成功。

2. OOPS: vsftpd: refusing to run with writable root inside chroot()
http://www.liquidweb.com/kb/error-500-oops-vsftpd-refusing-to-run-with-writable-root-inside-chroot-solved/
**解决**：
`sxz@ubuntuserver2:/$ sudo nano /etc/vsftpd.conf`
添加一句：`allow_writable_root=YES`

>**附录**：vsftpd配置解析
>/etc/vsftpd.conf
>- chroot：即change root directory （改变根目录），使用chroot后，系统的根目录将以指定的位置作为“/”位置。
>- chroot_local_user=YES 
将所有本地用户限制在自家目录中，NO则不限制。下面的指令是指出在所有本地用户中例外于本指令规定的用户。
>- local_umask=022
掩码，默认是777.  
http://bbs.chinaunix.net/thread-669631-1-1.html
>- local_root=/var/www
登录ftp后的根目录
