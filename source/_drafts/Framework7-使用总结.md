---
title: Framework7 使用小结
tags: [前端,WebApp,Framework7]
category: [WebApp]
---

## confirm 弹出两次的问题  
解决办法：绑定事件的方式，不要使用 `$$(document).on('eventname','selector',...)`，而是使用`$$('selector').on('click',function(){})`。   
参考：[Confirm modal appear twice after clicking any button](https://github.com/nolimits4web/Framework7/issues/1328)

## 序列化 form 表单元素  
Framework7 中没有提供 jQuery 的 form.serialize() 同名方法。但是提供类似的实现：  
``` javascript
window.myApp = new Framework7({ // .... });
var jsonObj = myApp.formToJSON('#formId');
```

<!--more-->

## 返回并刷新上一个页面
``` javascript
homeF7View.router.back({
    url: 'url',
    force: true,
    ignoreCache: true
});
```
或
``` javascript
homeF7View.router.back({
    url: 'modules/project/detail.html',
    context: contextData
    force: true,
    ignoreCache: true
});
```

## 跨页面回退并刷新
场景：页面跳转顺序是：“列表页-详情页-编辑页”。现在想在编辑页修改完数据，直接返回到列表页，并刷新列表数据。  
**遇到的问题：**   
返回到列表页后，将模版引擎生成的HTML附加到页面上，页面却是空白。  
原来是因为Framework7缓存了列表页的DOM结构，查找DOM时找到的是缓存的而且不可见的DOM节点。  
**解决：**   
将缓存的DOM元素移除，然后刷新HTML，并且不会影响回退效果。
```javascript
$$('div[data-page=payment-list]').remove();
```

## Tab页激活时也触发了`<div class="view">`的`show`事件


## ajax error:parseerror
使用DOM7发送Ajax请求，返回的JSON格式正确，但是总是报错提示：“error:parseerror”。
``` javascript
$$.ajax({
    url: url,
    method: options.method,
    data: options.data,
    dataType: 'json',
    success: function (data) {
        // 请求成功，调用回调函数
    },
    error: function (xhr, status) {
        // 接收到服务端响应后，总是执行到这里，status=parseerror
        console.log('ajax error:' + status);
    },
    complete: function (xhr, status) {
        console.log('ajax complete.');
    }
```
解决：  
将`dataType:'json'`去掉或修改为`text`，然后使用`JSON.parse(json)`手动解析服务端JSON数据。

## 无限滚动infinite-scroll 会触发多次监听事件
[API参考](http://framework7.taobao.org/docs/infinite-scroll.html#.WO2JIoh94WV)  
问题描述：  
页面往下滚动加载分页数据，但是页面到底部后，会连续触发多次监听事件，偶尔会重复请求同一页的数据。  
解决方案：  
其实Demo中已经给出了，就是设置一个loading作为flag，重复的触发会被return。

## 控制滚动条位置
``` javascript
$$('.page-content').scrollTop(0, 100);
```