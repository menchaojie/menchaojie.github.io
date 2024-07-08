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

### const
const关键字修饰常量，指明不可修改的意思，常用于修饰以下几种情况：
1. 变量，不可修改
2. 指针，* 之前 只想内容不可修改，* 之后 变量之前，指针不可修改，不能指向其他数据内容
3. 函数参数，函数参数不可修改
4. 成员变量，不可修改，常量使用，必须在构造函数的初始化列表中进行初始化
5. 成员函数，const放在在函数后，如`int getValue() const`，不可改变成员变量的值，提供只读访问接口；如果想要修改部分成员变量，可以和`mutable`结合
6. 对象，（如 const MyClass obj(10);），只能调用其 const 成员函数，而不能调用非 const 成员函数
7. 迭代器，
	- `std::vector<int>::iterator itr = xxx` 为正常模式，const iterator 
	- `const std::vector<int>::iterator itr = xxx`  const修饰了 `it`，相当于对指针进行了限定，指针为const，只能修改it指向的元素，不能进行`++itr`的操作，这种使用方法应该并不多用
	- `std::vector<int>::const_iterator itr = xxx`， 迭代器指向内容被限定，只读模式，不能修改迭代器指针指向的内容
	- C++11中使用auto获取迭代器有所不同，`auto & element:vec` 对应iterator， `const auto & element: vec` 对应const_iteration
8. 返回值，如`const Ratioanal operator *( xx,xx)`，这样避免返回值被修改或者复制，避免程序员想要想要写`(a*b)==c;`，但却写成`(a*b)=c`这样的错误。

### const 与static 联合修饰

在 C++ 中，关键字的顺序一般不影响声明的含义， 例如：

```cpp
#include <iostream>
class Example {
public:
    const static int a = 1;   // 正确
    const int static b = 2;   // 正确
    static const int c = 3;   // 正确
    static int const d = 4;   // 正确
    int const static e = 5;   // 正确
    int static const f = 6;   // 正确
};
int main() {
    std::cout << Example::a << std::endl;  // 输出 1
    std::cout << Example::b << std::endl;  // 输出 2
    std::cout << Example::c << std::endl;  // 输出 3
    std::cout << Example::d << std::endl;  // 输出 4
    std::cout << Example::e << std::endl;  // 输出 5
    std::cout << Example::f << std::endl;  // 输出 6
    return 0;
}
```

但要注意的是，当出现指针的声明时，要特别留意，* 号前后关键字修饰的对象是不同的。
