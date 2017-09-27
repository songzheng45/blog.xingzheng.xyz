---
title: Travis 自动构建 Hexo 并提交到 GitHub 分支
tags: [Travis,Hexo]
category: 
---

### 问题：Travis 没有构建
问题： 提交后，发现 Travis 构建历史是空的。     
解决： 使用 GitHub 账号登录 `travis-cli.org`，打开 Travis 关联的仓库，切换到 `Current` 选项卡，点击`Activite`。   

<!--more-->

### 问题：构建后，提交到 gh-pages 分支时出错
查看 Travis 构建日志，最后提交到分支的时候，提示 error：   
```
$ git push --quiet --force https://$GH_Token@github.com/songzheng45/xingzheng.me.git gh-pages
error: src refspec gh-pages does not match any.
error: failed to push some refs to 'https://bc4e15d6a2cf67d9acc958c2cb6acda34914dd80@github.com/songzheng45/xingzheng.me.git'
```
原因：  
`git push` 命令有误，应该是：
```
git push --quiet --force https://$GH_Token@github.com/songzheng45/xingzheng.me.git master:gh-pages
```
最后的`master:gh-pages`，指定将本地的 `master` 分支提交到远程仓库的 `gh-pages`分支。   
这里 Travis 构建后进入 ./publish 文件夹 ，初始化git仓库，然后提交到我们的 GitHub 仓库的指定分支。   