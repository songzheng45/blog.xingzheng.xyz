---
title: ASP.NET MVC 启用 CORS
tags:
  - ASP.NET MVC
  - CORS
category: 技术
date: 2017-09-27 17:24:15
updated: 2017-09-27 17:24:15
---


浏览器出于安全性考虑, 会阻止 AJAX 请求其他域名下的资源. 这个限制叫作 `same-origin`(同源) 策略.  

### 什么是同源?
如果两个 URL 有相同的scheme、域名和端口, 他们就有相同的来源.

以下两个 URL 是同源的:
- `http://example.com/foo.html`
- `http://example.com/bar.html`

以下这些 URL 相比上面两个是非同源的:
- `http://example.net`, 不同的域名
- `http://www.example.com/foo.html`, 不同的子域名
- `https://example.com/foo.html`, 不同的 scheme
- `http://example.com:9000/foo.html`, 不同的端口

 
例如, 在 `www.xingzheng.me/books` 页面上通过 AJAX 访问 `www.xingzheng.me/api/blogs` 是没问题的. 但是访问 `api.xingzheng.me/blogs` 就会失败, 浏览器控制台会输出如下错误:
```
XMLHttpRequest cannot load http://api.xingzheng.me/blogs. No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://www.xingzheng.me' is therefore not allowed access.
```

>同源策略不会阻止浏览器发送请求, 但是会阻止应用程序"看到"响应内容.  
同源策略能够阻止恶意网站读取其他网站的敏感数据.  

<!--more-->

### 什么是 CORS

[Cross Origin Resource Sharing][1](CORS) 是一个 w3c 标准, 允许服务器放宽 `same-origin` 策略. 使用 CORS , 服务器可以在允许一些"跨源"的请求的同时拒绝其他的请求. CORS 比早期的技术如 JSONP 更安全、更灵活.

## CORS 是如何工作的?

CORS 规范介绍了几个新的 HTTP 头来允许 `cross-origin` 请求. 如果浏览器支持 CORS , 它会自动设置这些 HTTP 头, 而我们在 Javascrpit 代码中什么也不用做.


例如, 以下是一个跨域请求的请求头信息:
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

### 预检请求
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

## ASP.NET MVC5 启用 CORS
在 ASP.NET MVC5 中支持 CORS 有三种方式:
- 自定义 ActionFilterAttribute
- Web.config 配置 `httpProtocol/customHeaders`

### 自定义 ActionFilterAttribute
```
public class AllowCORSAttribute : ActionFilterAttribute
    {
        /// <summary>Called by the ASP.NET MVC framework before the action method executes.</summary>
        /// <param name="filterContext">The filter context.</param>
        public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            // 支持多个域名的跨域请求
            List<string> domains = new List<string>() { "http://domain1.com", "http://domain2.com" };
            var host = filterContext.HttpContext.Request.UrlReferrer.Host;


            var filterHost = domains.FirstOrDefault(x => x.Contains(host));
            if (filterHost != null)
            {
                // 允许跨域请求的域名
                filterContext.HttpContext.Response.Headers.Add("Access-Control-Allow-Origin", filterHost);

                // 如果请求要传递cookie或其他认证方案所需的信息, 需要设置 Access-Control-Allow-Credentials 为 true
                filterContext.HttpContext.Response.Headers.Add("Access-Control-Allow-Credentials", "true"); 
            }

            base.OnActionExecuting(filterContext);
        }
    }
```    

### Web.config 配置 `httpProtocol/customHeaders`
```xml
<system.webServer>
  <httpProtocol>
    <customHeaders>
      <clear />
      <add name="Access-Control-Allow-Origin" value="http://domain1.com" />
      <add name="Access-Control-Allow-Credentials" value="true" />     
    </customHeaders>
  </httpProtocol>
</system.webServer>
```


## 参考

[在 ASP.NET WebAPI2 中启用 CORS][2]   
[在 ASP.NET Core 中启用 CORS][3]

[1]:http://www.w3.org/TR/cors/
[2]:https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/enabling-cross-origin-requests-in-web-api
[3]:https://docs.microsoft.com/en-us/aspnet/core/security/cors

