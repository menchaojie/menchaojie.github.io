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

## const , static ==> 变量修饰词
又这两个关键字想到的一些事情，在此之前先介绍一下这两个关键字的基础用法

### static

static 关键字主要用于控制变量的**存储持续时间**和**作用域**：

 -  **静态局部变量**：在函数内部声明，存储持续时间为整个程序生命周期，作用域仅限于声明它的函数内。
 -   **静态全局变量**：在全局作用域声明，存储持续时间为整个程序生命周期，作用域仅限于声明它的文件内。
 -   **静态类成员变量**：在类内部声明，存储持续时间为整个程序生命周期，所有类的对象共享同一个静态成员变量。

其中，静态成员变量不属于任何对象，属于类，或者属于所有对象共享，其定义和初始化要在类的外部，其例如下：
```cpp
#include <iostream>
class Example {
public:
    static int count; // 静态类成员变量

    Example() {
        count++;
    }
};

int Example::count = 0; // 静态成员变量定义并初始化

int main() {
    Example e1;
    Example e2;
    Example e3;

    std::cout << "Count: " << Example::count << std::endl; // 输出 3
    return 0;
}
```