---
title: ASP.NET MVC5 Model 绑定之 ValueProvidedr
tags:
    - ASP.NET MVC
    - Model Binder
    - ValueProvider
category: 技术
---

Model 绑定旨在为目标 Action 方法的执行生成参数列表，分为以下过程：
1. 构建参数所需的原始数据（由 **ValueProvider** 提供）
2. 执行绑定操作（由 **ModelBinder** 完成）

这篇文章总结ASP.NET MVC5 是如何利用`ValueProvider`对象来提供源数据的。  

<!--more-->

一个 ValueProvider 对象是对一个数据容器的封装，该容器一般具有类似字典的数据结构。  
所有的ValueProvider类型均实现了接口 [IValueProvider][IValueProvider]。如下代码段所示：  
利用`GetValue`方法用指定的Key从数据容器中取出对应的数据，而这个Key和与容器中对应数据条目的Key可能并非完全一致，后者可能在前者基础上添加相应的前缀，而`ContainsPrefix`方法就是用于判断数据源容器中是否包含指定前缀的Key。
``` csharp
/// <summary>Defines the methods that are required for a value provider in ASP.NET MVC.</summary>
public interface IValueProvider
{
    bool ContainsPrefix(string prefix);
    ValueProviderResult GetValue(string key);
}
```
Controller中可以调用基类ControllerBase的ValueProvider属性进行获取和设置。  
其中`ValueProviderResult`的定义是：
``` csharp
```

## NameValueCollectionValueProvider

### 两种前缀形式
描述复杂类型的`ModelMetadata`对象具有树形层次化结构，但是作为数据源容器的`NameValueCollection`对象却是一个“扁平”结构,两者之间的匹配通过前缀来实现。  
1. 点号(.)  
将点号作为分隔符的前缀可以用于：  
- 表示基于属性的层级关系
- 数据筛选
2. 索引  
针对目标类型是数组或集合的数据源元素，用方括号“[]”表示。  
索引可以是数字或文字，两种不同形式的索引对应着不同的Model绑定机制。  










[IValueProvider]: https://msdn.microsoft.com/en-us/library/system.web.mvc.ivalueprovider(v=vs.118).aspx
