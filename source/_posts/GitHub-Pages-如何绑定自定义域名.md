---
title: GitHub Pages 如何绑定自定义域名
date: 2017-03-12 20:22:00
tags: [GitHub Pages, 域名]
---

### 利用 GitHub 添加 CNAME
进入GitHub仓库，在 `Settings` 选项卡下找到 `GitHub Pages`，`Source` 那里填写 GitHub Pages 所在的分支，然后填写下面的 `Custom domain`，点击`Save`。

>也可以手动在代码库中提交一个CNAME文件，值就是你的域名。


### 添加域名解析
在域名解析提供商控制台，添加域名解析，下面以 万网 为例。
1. 先添加一个CNAME，主机记录写`@`，后面记录值写上你的 `http://username.github.io`
2. 再添加一个CNAME，主机记录写 `www`，后面记录值也是 `http://username.github.io` 
作用就是，访问你的域名会指向`http://username.github.io`，然后 GitHub 会根据 GitHub Pages 所在的分支里的 CNAME，返回页面。
