---
title: InnoSetup 使用小结
tags:
  - InnoSetup
date: 2017-03-15 00:21:53
updated: 2017-03-15 15:05:08
category:
---


>背景  
接上篇[Could not load file or assembly System, Version=2.0.5.0 in .NET 4][loadsystem]。  
由于.NET Framework 4.0补丁包，区分 X86、 X64 和 IA64，而客户很难区分自己的电脑是多少位的，因此产品同事希望能在技术上解决。  
于是查了下 Inno Setup 的相关资料，脚本语言 Pascal。但是毕竟没有深入研究，仅限于这次的需求。 

[InnoSetup中文帮助.pdf](uploads/InnoSetup中文帮助.pdf)

## 检查系统是32位还是64位
- 方法一：
检查是64位系统： `Check: IsWin64;`
检查是32位系统： `Check: "not IsWin64";`

- 方法二：
``` pascal
if IsWin64 then 
begin 
    MsgBox('must be x64', mbInformation, MB_OK);
end
else
begin
    MsgBox('x86', mbInformation, MB_OK);
end
```

<!--more-->

## 判断是否已安装 .NET Framework 4.0
``` pascal
function FrameworkIsNotInstalled: Boolean;
begin
  Result :=
    not RegKeyExists(
      HKEY_LOCAL_MACHINE, 'SOFTWARE\Microsoft\.NETFramework\policy\v4.0');
end;
```

## 根据系统环境安装 .NET Framework 4 以及补丁包
```
[Files]
;如果没有安装 .NET Framework，则将 Microsoft-NET-40.exe 解压到系统临时目录
Source: "C:\DongfangCaiwuSetupFiles\netfx\Microsoft-NET-40.exe"; DestDir: "{tmp}"; Flags: ignoreversion;Check: FrameworkIsNotInstalled;

; 如果系统是32位，则将32位补丁包解压到系统临时目录
Source: "C:\DongfangCaiwuSetupFiles\netfx\NDP40-KB2468871-v2-x86.exe"; DestDir: "{tmp}"; Flags: ignoreversion;Check: "not IsWin64";

; 如果系统是64位，则将64位补丁包解压到系统临时目录
Source: "C:\DongfangCaiwuSetupFiles\netfx\NDP40-KB2468871-v2-x64.exe"; DestDir: "{tmp}"; Flags: ignoreversion;Check: IsWin64;


[Run]
;如果没有安装 .NET Framework，则运行 Microsoft-NET-40.exe
Filename: "{tmp}\Microsoft-NET-40.exe"; Check: FrameworkIsNotInstalled

; 如果系统是32位，则运行32位补丁包
Filename: "{tmp}\NDP40-KB2468871-v2-x86.exe";  Check: "not IsWin64";

; 如果系统是64位，则运行64位补丁包
Filename: "{tmp}\NDP40-KB2468871-v2-x64.exe"; Check: IsWin64;

[Code]

// 函数，判断系统是否已安装 .NET Framework 4.0
function FrameworkIsNotInstalled: Boolean;
begin
  Result :=
    not RegKeyExists(
      HKEY_LOCAL_MACHINE, 'SOFTWARE\Microsoft\.NETFramework\policy\v4.0');
end;
```

***
### 参考
[Installing .NET Framework 4.5 automatically with Inno Setup](https://blogs.msdn.microsoft.com/davidrickard/2015/07/17/installing-net-framework-4-5-automatically-with-inno-setup/)  
[Check .NET Version with Inno Setup](http://www.kynosarges.de/DotNetVersion.html)




[loadsystem]:2017/03/15/Could-not-load-file-or-assembly-System-Version-2-0-5-0-in-NET-4/