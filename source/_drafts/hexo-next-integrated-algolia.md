---
title: Hexo+Next 集成站内搜索 Algolia
tags: 
    - Hexo
    - Next
    - Algolia
category: 
---

## 注册algolia
打开[注册页面](https://www.algolia.com/users/sign_up)，选择使用邮箱或者使用Github、Google帐户创建一个帐号。  

登录进去后，默认进入`Dashboard`页面。点击左侧菜单的`Indices`，再点击右上角的`New Index`，创建新索引。

<!--more-->

## 配置并安装algolia
编辑站点配置`_config.yml`

## 配置Next主题
在 `themes/next/_config.yml` 文件, 找到 `algolia_search`节点, 如下:
```yaml
algolia_search:
  enable: true
  hits:
    per_page: 15
  labels:
    input_placeholder: 输入搜索关键词
    hits_empty: "找不到任何搜索结果: ${query}"
    hits_stats: "找到 ${hits} 个搜索结果, 耗时: ${time} ms"
```
`enable`设为true, `per_page`每页多少个搜索结果, 其它根据需要修改.

## 解决遇到的问题

### Travis 构建时报错 : Cannot find module 'bluebird'
``` bash
$ hexo clean
ERROR Plugin load failed: hexo-algolia
Error: Cannot find module 'bluebird'
```
安装 bluebird 即可解决：  
``` bash
npm install --save bluebird
```

### Travis 构建时报错 : AlgoliaSearchError: Operations quota exceeded
使用 Travis 构建时报错：  
```bash
/home/travis/build/xxxx/node_modules/hexo-algolia/lib/command.js:85

      throw err;

      ^

AlgoliaSearchError: Operations quota exceeded, change plan to get more Operations.

    at success (/home/travis/build/songzheng45/xingzheng.me/node_modules/hexo-algolia/node_modules/algoliasearch/src/AlgoliaSearchCore.js:335:32)

    at process._tickCallback (node.js:369:9)
```
打开[Algolia官网](https://www.algolia.com/)，登录进入Dashboard，发现 Algolia 改版了，必须选择一个付费计划，选择`Go Free`。  
回到 Travis 再次构建成功。


### Travis 构建时报错: 需要设置 Algolia INDEXING_KEY

```bash
ERROR [Algolia] Please set an `HEXO_ALGOLIA_INDEXING_KEY` environment variable to enable content indexing
```

解决办法：

在 Travis 项目下，依次打开 `More Options`-`Settings`，在 `Environment Variables` 下添加一项键值对：

| HEXO_ALGOLIA_INDEXING_KEY | Algolia API KEY |
| :-----------------------: | :-------------: |
|                           |                 |

其中 `Algolia API KEY` 填写 Alogolia 网站上生成的 `Admin Key`, 否则构建时还会报错: `Not enough rights to update an object`.

### Travis 构建时报错: [Algolia] Invalid Application-ID or API key

解决办法:

原因是我在 Algolia 后台重新生成了 Admin Api Key, 需要删除旧的, 重新添加一条.    

具体参考上一个问题.
