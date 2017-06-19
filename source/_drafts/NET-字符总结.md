---
title: .NET char(字符)总结
tags: 
    - .NET
    - char
category: 技术
---

.NET Framework 中字符总是表示成16位 Unicode 代码值，用 `System.Char` 结构的一个实例来表示一个字符。  

`System.Char`提供两个公共只读常量字段：`MinValue`和`MaxValue`。  

静态方法 `GetUnicodeCategory` 的返回值是一个 `UnicodeCategory` 枚举值，指出该字符是控制字符、货币符号、小写字母、大写字母、标点符号、数学符号、还是其他字符（由Unicode标准定义）。**IsDigit**、**IsLetter** 等大多数静态方法内部都调用了 `GetUnicodeCategory`。  
``` csharp
public static UnicodeCategory GetUnicodeCategory (string s, int index);
public static UnicodeCategory GetUnicodeCategory (char c);
```

<!--more-->

`ToLowerInvariant`和`ToUpperInvariant` 方法会忽略语言文化，将一个字符转换为它的小写或大写形式。  
``` csharp
public static char ToLowerInvariant (char c);
public static char ToUpperInvariant (char c);
```
`ToLower`和`ToUpper`可以替代以上两个方法，但是在转换时会使用和线程关联的语言文化信息（根据`System.Threading.Thread`的`CurrentCulture`属性获取），或者向方法传递一个 CultureInfo 对象。
``` csharp
public static char ToUpper (char c, CultureInfo culture);
public static char ToUpper (char c);
public static char ToLower (char c, CultureInfo culture);
public static char ToLower (char c);
```

