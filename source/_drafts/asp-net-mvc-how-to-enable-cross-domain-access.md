---
title: ASP.NET MVC 启用 CORS
tags: [ASP.NET MVC, CORS]
category: 技术
---

## CORS 简介

浏览器出于安全性考虑, 会阻止 AJAX 请求其他域名. 这个限制叫作 `same-origin` 策略.  
 
例如, 当前页面是 `www.xingzheng.me`, 在该页面上通过 AJAX 访问 `www.xingzheng.me/api/blogs` 是没问题的. 但是访问 `api.xingzheng.me/blogs` 是没有响应内容的, 而且浏览器控制台会输出如下错误:
```
XMLHttpRequest cannot load http://api.xingzheng.me/blogs. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://www.xingzheng.me' is therefore not allowed access.
```

>`same-origin` 策略不会阻止浏览器发送请求, 但是会阻止应用程序"看到"响应内容.  
`same-origin` 策略能够阻止恶意网站读取其他网站的敏感数据.  

<!--more-->


[Cross Origin Resource Sharing][1](CORS) 是一个 w3c 标准, 允许服务器放宽 `same-origin` 策略. 使用 CORS , 服务器可以在允许一些"跨源"的请求的同时拒绝其他的请求. CORS 比早期的技术如 JSONP 更安全、更灵活.

## CORS 是如何工作的?

CORS规范介绍了几个新的 HTTP 头来允许 `cross-origin` 请求. 如果浏览器支持 CORS , 它会自动设置这些 HTTP 头, 而我们在 Javascrpit 代码中什么也不用做.


例如, 以下是一个跨域请求头的例子:
```
GET http://www.caiwu.com/Message/CheckUnreadMessage?ajax=1 HTTP/1.1
Host: www.caiwu.com
Origin: http://m.caiwu.com
Accept: */*
Referer: http://m.caiwu.com/index.html
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.8,en;q=0.6,zh-TW;q=0.4,en-US;q=0.2
```
`Origin` 的值就是发出请求的域名.

服务服务器如果允许本次请求, 则在响应头中设置 `Access-Control-Allow-Origin` 头, 它的值就是上面 `Origin` 头的值(如果是 `*` 表示允许来自所有的 `origin` 的请求).  
```
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Access-Control-Allow-Origin: http://m.caiwu.com
Date: Wed, 27 Sep 2017 02:47:18 GMT
```
如果响应头中不包含 `Access-Control-Allow-Origin`, 表示服务器不允许跨域请求, 则请求失败, 浏览器会明确指出不允许该请求. 即使服务器返回成功(200), 浏览器也不会让客户端看到响应内容.  

## 预检请求
对一些 CORS 请求,在向资源发出真正的请求之前, 浏览器会发送一个额外请求, 叫作"预检请求".

先看个例子, 以下是预检请求的请求头:
```
OPTIONS http://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: http://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

预检请求使用 HTTP OPTIONS 方法, 它包含两个特殊的请求头: 
- Access-Control-Request-Method  
    后续实际请求中使用的 HTTP 方法.
- Access-Control-Request-Headers  
    一个请求头列表, 包含服务端应用程序在实际请求中设置的(不包含浏览器设置的头).

预检请求的响应头, 如下:
```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: http://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 05 Jun 2013 06:33:22 GMT
```
响应包含的 `Access-Control-Allow-Methods` 列出允许的方法, 和一个可选的 `Access-Control-Allow-Headers` 头, 列出允许的头.  
如果预检请求成功, 浏览器会发送实际的请求.


### CORS 新增的响应头
CORS 规范新增的响应头如下:

- Access-Control-Allow-Origin  
    允许请求的其他域名. 如果是`*`表示允许所有其他域名的请求.
- Access-Control-Allow-Credentials  
    请求是否需要证书. 默认情况下, 浏览器不会发送任何证书信息, 包括 cookie 和 HTTP 认证方案. 为了在跨域请求中发送证书, 在客户端和服务器端需要同时设置该值为true.  
    注意, 如果未包含该请求头, 或值为false, 那么服务器端将不能读取session, 因为客户端没有发送cookie.
- Access-Control-Allow-Methods  
    服务器端允许处理的跨域请求方法. 如 GET/POST/PUT 等.  
    如果要允许所有的 HTTP 方法, 则使用通配符 `*`.
- Access-Control-Allow-Headers  
    服务器端允许发送给客户端的响应头. 




[1]:http://www.w3.org/TR/cors/

