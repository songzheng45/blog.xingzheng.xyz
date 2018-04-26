---
title: Hexo简明使用
tags: Hexo
category: 技术
date: 2017-04-21 23:44:37
updated: 2017-04-21 23:44:37
---


## 什么是 Hexo？
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。

## 安装Hexo
前提要先安装 node 和 Git，然后使用npm安装Hexo：  
```
$ npm install -g hexo-cli
```

## 使用Hexo
### 初始化
```
$ hexo init
```

### 新建文章
```
$ hexo new <layout> <title>
$ hexo new <title>
```
layout 即模板, 可选，如果省略则默认是 `post`, 即新建文章.   
存放路径在： `source/_posts`。

<!--more--> 

### 新建草稿
```
$ hexo new draft <title>
```
草稿默认不显示在博客首页，存放路径在 `source/_drafts`。  

### 发布草稿
```
$ hexo publish [layout] <title>
```

### 新建页面
```
$ hexo new page <title>
```
如 `hexo new page "tags"` 新建`tags`页面，路径在：`source/tags`。

### 修改默认布局
修改`_config.yml`中的`default_layout`配置项，修改为 `scaffolds/`下的某个布局文件名即可。

### Hexo Server
参考: [Hexo Server][1]

Hexo 3.0 把服务器独立成了个别模块，必须先安装 hexo-server 才能使用。
```
$ npm install hexo-server --save
```

安装完成后，输入以下命令以启动服务器，您的网站会在 http://localhost:4000 下启动。在服务器启动期间，Hexo 会监视文件变动并自动更新，您无须重启服务器。
```
$ hexo server
或
$ hexo s
```
>默认端口4000与Foxit Reader(福昕PDF阅读器）foxitprotect.exe占用端口冲突, 如果启动后无法访问, 请修改端口号.

- **自定义端口**
 `hexo server -p 5000`  
- **静态模式**
 `hexo server -s`, 只处理`public`文件夹内的文件,不会处理文件变动, 需要先执行`hexo generate`. 通常用于生产环境下.  
- **自定义 IP**
 `hexo server -i 192.168.1.1`

### 部署
```
$ hexo deploy
```
可选项：`-g, --generate`，在部署前生成文件.

### 其他选项
- 调试模式
```
$ hexo --debug
```
- 显示草稿
```
$ hexo --draft
```
例如, `hexo server --draft` 会启动 Hexo Server 来预览草稿.

参考：[Hexo概述](https://hexo.io/zh-cn/docs/index.html)

## Hexo-Next 使用
### 创建标签云
[参考这里](https://github.com/iissnan/hexo-theme-next/wiki/%E5%88%9B%E5%BB%BA%E6%A0%87%E7%AD%BE%E4%BA%91%E9%A1%B5%E9%9D%A2)

多标签的格式，在 `front-master` 中： 
```
---
tags: [tag1,tag2,tag3]
---
```
或
```
---
tags: 
    - tag1
    - tag2
    - tag3
---
```

### 创建分类  
[参考这里](https://github.com/iissnan/hexo-theme-next/wiki/%E5%88%9B%E5%BB%BA%E5%88%86%E7%B1%BB%E9%A1%B5%E9%9D%A2)


### 分类/标签数量不准确
清理并重新生成：  
```
$ hexo clean
$ hexo generate
```

### 如何在首页开启“阅读更多”
在文章中加 `<!--more-->` 进行截断。



## Travis 自动构建 Hexo

`.travis.yml`示例：

```yaml
language: node_js
node_js: node

before_install:
  - git config --global push.default matching
  - git config --global user.name "john" 
  - git config --global user.email "john@gmail.com"

install:
  - npm install hexo-cli -g
  - npm install hexo --save
  - npm install
  - npm install hexo-generator-feed --save
  - npm install hexo-deployer-git --save
  - npm install hexo-renderer-ejs --save
  - npm install hexo-renderer-stylus --save
  - npm install hexo-renderer-marked --save
  - npm install hexo-renderer-sass --save
  - npm install hexo-algolia --save

script:
  - hexo clean
  - hexo generate
  - hexo algolia

after_success:
  - cd ./public
  - git init
  - git add --all .
  - git commit -m "Travis CI Auto Builder"
  - git push --quiet --force https://$GH_Token@github.com/john/johnsblog.git master:gh-pages

branches:
  only:
    - master

#cache:
  #directories:
    #- node_modules

```



### Travis 构建问题

1. Cannot download node-sass

   ```bash
   > node-sass@4.8.3 install /home/travis/build/songzheng45/xingzheng.me/node_modules/node-sass
   > node scripts/install.js

   Downloading binary from https://github.com/sass/node-sass/releases/download/v4.8.3/linux-x64-64_binding.node
   Cannot download "https://github.com/sass/node-sass/releases/download/v4.8.3/linux-x64-64_binding.node": 

   HTTP error 404 Not Found

   Hint: If github.com is not accessible in your location
         try setting a proxy via HTTP_PROXY, e.g. 

         export HTTP_PROXY=http://example.com:1234

   or configure npm proxy via

         npm config set proxy http://example.com:8080
         
   ......      

   npm ERR! Failed at the node-sass@4.8.3 postinstall script.
   ......
   ```

   node-sass 的一个 issue [Can't install node-sass with NPM 5.0.0][2] 提到该问题，其中一个贡献者说该问题超出了他们能处理的范围，建议将 npm 降级到4.0





## 问题
### Cannot find module 'hexo-util' 
``` bash
$ hexo clean
ERROR Script load failed: themes/next/scripts/tags/exturl.js
Error: Cannot find module 'hexo-util'
```
``` bash
$ hexo generate
ERROR Script load failed: themes/next/scripts/tags/exturl.js
Error: Cannot find module 'hexo-util'
```
执行以下命令：  
``` bash
npm install hexo-util --save
```



[1]:https://hexo.io/zh-cn/docs/server.html
[2]:https://github.com/sass/node-sass/issues/1991	"Can&#39;t install node-sass with NPM 5.0.0 #1991"