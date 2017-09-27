---
title: Hexo+Next集成站内搜索algolia
tags: 
    - Hexo
    - Next
    - Algolia
category: 
---

## 注册algolia
打开[注册页面](https://www.algolia.com/users/sign_up)，选择使用邮箱或者使用Github、Google帐户创建一个帐号。  

登录进去后，默认进入`Dashboard`页面。点击左侧菜单的`Indices`，点击右上角的`New Index`，创建新索引。

<!--more-->

## 配置并安装algolia
编辑站点配置`_config.yml`

## 配置Next主题


## 问题

### Cannot find module 'bluebird'
``` bash
$ hexo clean
ERROR Plugin load failed: hexo-algolia
Error: Cannot find module 'bluebird'
```
安装 bluebird 即可解决：  
``` bash
npm install --save bluebird
```

### AlgoliaSearchError: Operations quota exceeded
使用 Travis 构建时报错：  
```bash
/home/travis/build/xxxx/node_modules/hexo-algolia/lib/command.js:85

      throw err;

      ^

AlgoliaSearchError: Operations quota exceeded, change plan to get more Operations.

    at success (/home/travis/build/songzheng45/xingzheng.me/node_modules/hexo-algolia/node_modules/algoliasearch/src/AlgoliaSearchCore.js:335:32)

    at process._tickCallback (node.js:369:9)
```
打开[Algolia官网](https://www.algolia.com/)，登录进入Dashboard，发现algolia改版了，必须选择一个付费计划，选择`Go Free`。  
再次回到travis构建成功。