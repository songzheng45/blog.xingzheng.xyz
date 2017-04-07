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
    ignoreCache: true,
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

## Tab页激活时也出发了`<div class="view">`的`show`事件
