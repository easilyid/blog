---
title: Xlua
author: Heart
date: 2023-04-04 15:50:58
categories: Lua 
tags:
  - Lua
  - 热更新
  - Xlua
  - Unity
index_img: https://cdn.jsdelivr.net/gh/easilyid/Photo/photo/20230404155135.png
excerpt: XLua CSharpCallLua LuaCallCSharp
---

# XLua

xLua是用来实现Lua代码与C#代码相互调用的插件。我们可以借助这个插件来实现热更新方案。

可以运行时把C#实现（方法，操作符，属性，事件，构造函数，析构函数，支持泛化）替换成lua实现
为Unity提供Lua编程能力，让C#和Lua可以相互调用访问

热更新原理：

![](https://cdn.jsdelivr.net/gh/easilyid/Photo/photo/20230326103203.png)

> 万物 Hello World

在C#中运行Lua代码首先我们要创建Lua运行环境，XLua中有一个LuaEnv可供我们使用

```c#
  LuaEnv luaEnv = new LuaEnv();
  //打印字符串 可传入三个参数 内容, 哪个脚本, 哪个解析器   
  luaEnv.DoString("print('HelloWord')");
```

XLua常用方法：

- DoString
