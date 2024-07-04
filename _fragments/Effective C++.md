---
layout: fragment
title: Effective C++
tags: [C++, effective]
description: 一些c++的知识点
keywords: c++, effective
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## C++中的字符串常量

对于下面的代码：
```cpp
char s[] = "hello,world";
```
在 C++ 中，这是一种`定义`字符数组并进行`初始化`的方式。详细说来：
1. `char s[]` ：声明了一个字符数组 `str` 。由于在初始化时指定了初始值，所以数组的大小会根据初始值的长度自动确定。
2. `"hello, world"` ：这是一个字符串常量。在这个初始化中，字符串 `"hello, world"` 的内容会被逐个字符地复制到字符数组 `str` 中。
需要注意的是，字符串常量会在末尾自动添加一个空字符 `'\0'` 作为字符串的结束标志。所以，实际上数组 `str` 的大小是字符串 `"hello, world"` 的长度加上 1（用于存储结束标志 `'\0'` ）。

所以重点是，`"hello,world"`作为字符串常量，本身是含有一个隐含的`’\0‘`的，

至于初始化，这里可以思考一下对象创建时调用复制构造函数的情景。