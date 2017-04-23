---
title: Hexo+Next集成站内搜索algolia
tags: 
    - Hexo
    - Next
    - Algolia
category: 
---

## 注册algolia


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