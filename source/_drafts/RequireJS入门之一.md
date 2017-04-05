---
title: RequireJS 入门(一)第一个实例
tags: [RequireJS]
category: 前端
---

## 什么是 RequireJS
RequireJS的目标是鼓励代码的模块化，它使用了不同于传统`<script>`标签的脚本加载步骤。可以用它来加速、优化代码，但其主要目的还是为了代码的模块化。它鼓励在使用脚本时以module ID替代URL地址。

## 第一个 Demo
我们先从一个最简单的Demo入手，使用最少的配置，了解如何使用 RequireJS。  
这个demo里我们封装一个选择器模块`selector.js`，然后在`main.js`中使用。  

<!--more-->


demo的目录结构如下：  
```
| -- index.html
| -- scripts
| ---- require.js
| ---- main.js
| ---- selector.js
```
其中 index.html 的内容如下：  
``` html
<body>
    <p id="tomato">番茄</p>
    <p class="apple">苹果</p>
    <span>土豆</span>

    <script data-main="scripts/main" src="scripts/require.js" type="text/javascript"></script>
</body>
```

Let's go!

### 1. 引入 RequireJS
``` javascript
<script data-main="scripts/main" src="scripts/require.js"></script>
```
与传统引入js的方式略有不同，我们引入`scripts/require.js`，并且`<script>`标签有一个`data-main`属性，该属性表示`scripts`目录下的`main.js`作为页面的入口js脚本，可以理解为C、Java或C#中的Main函数，并且用异步加载的方式加载`main.js`。

### 2. 实现 selector.js
```javascript
define(function () {
    return {
        // 根据Id获取元素
        getById:function(id){
            return document.getElementById(id);
        },
        // 根据class名称获取元素
        getByClass:function(className){
            return document.getElementsByClassName(className);
        },
        // 根据标签名称获取元素
        getByTag:function(tagName){
            return document.getElementsByTagName(tagName);
        }
    };
});
```
使用 `define` 关键字定义一个模块，并返回一个对象，该对象包含三个函数。  
实际上模块可以返回对象，也可以直接返回一个函数。

### 3. 在 main 中使用 selector 模块
实现`main.js`，如下：
```javascript
require(["selector"], function (selector) {
    console.log(selector.getById("tomato").innerText);
    console.log(selector.getByClass("apple")[0].innerText);
    console.log(selector.getByTag("span")[0].innerText);
});
```
`require`有两个参数，第一个是依赖模块，Array类型；第二个参数是回调函数，参数就是所依赖的模块对象，然后在回调函数内部就可以使用模块对象了。  
如果依赖多个模块，用法如下：
```javascript
require(["cat","dog"],function(cat,dog){
    // 在这里就可以使用 cat、dog对象了
})
```
回调函数的参数顺序，和依赖的模块顺序相同。

运行index.html，可以在console里看到输出。  

Ok，到这里我们就完成了一个非常简单的demo，虽然没有什么实用价值，但是对 RequireJS 有点感觉了吧。

## 总结
- RequireJS 鼓励代码的模块化，并使用异步方式加载模块
- 使用 `define()` 自定义模块
- 使用 `require()`引用依赖的模块