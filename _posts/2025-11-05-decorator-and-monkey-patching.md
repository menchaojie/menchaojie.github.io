---
layout: post
title: 装饰器和猴子补丁（monkey patching)
categories:
  - python
description: 学一些python知识
keywords:  
  - python
  - monkey patching
  - decorator
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

**Monkey Patching 与装饰器（Decorator）**  都有更改原来程序或函数行为的功能，但二者也有很大的不同


# Monkey Patching 与 Decorator：动态修改的两种姿势

在动态语言（尤其是 Python）中，我们有时需要在不修改源码的情况下，**改变或扩展函数、类的行为**。  
有两种常见方式可以做到这一点：

- **Monkey Patching（猴子补丁）**  
- **Decorator（装饰器）**

虽然它们都能改变行为，但在**时机、范围和安全性**上有显著差异。

---

## 🐒 什么是 Monkey Patching

**Monkey Patching（猴子补丁）** 指的是在**程序运行时（runtime）**，**动态修改**或**替换**已有类、函数或模块中的方法。  
这通常用于修复第三方库的 bug，或者临时改变行为。

```python
# 假设我们有一个第三方模块
class Dog:
    def bark(self):
        return "woof"

# 我们不能改源码，但可以“打补丁”
def new_bark(self):
    return "meow?"

Dog.bark = new_bark  # Monkey patch

print(Dog().bark())  # 输出：meow?
````

这种方法确实灵活，但风险也大。
一旦修改，全局行为都会改变，其他依赖 `Dog` 的代码可能被意外影响。

因此，一般安全的Monkey Patching 使用方法是：
```
1. 本分原来的方法
2. 替换原来的方法为新方法
3. 使用方法
4. 将方法恢复为原来的方法
```

---

## 🎨 什么是 Decorator（装饰器）

**装饰器** 是在**定义函数或类时**进行的修改，而不是运行时偷偷替换。
它通过**包装函数**的方式添加新行为，更加安全、可控。

```python
def log_call(func):
    def wrapper(*args, **kwargs):
        print(f"调用函数：{func.__name__}")
        return func(*args, **kwargs)
    return wrapper

@log_call
def greet():
    print("你好")

greet()
# 输出：
# 调用函数：greet
# 你好
```

装饰器的修改是**显式的**，一眼就能看出来，不会神秘地改变全局行为。

---

## ⚖️ Monkey Patching 与 Decorator 的对比

| 对比项     | Monkey Patching   | Decorator        |
| :------ | :---------------- | :--------------- |
| 修改时机    | 运行时（runtime）      | 定义时（import 或装饰时） |
| 修改范围    | 全局（影响所有引用）        | 局部（仅作用于装饰的目标）    |
| 可控性     | 难追踪、易出错           | 明确、安全            |
| 常见用途    | 热修复、测试 mock、第三方修补 | 日志、权限控制、缓存等功能扩展  |
| 是否破坏原代码 | 是                 | 否                |

猴子补丁像是在系统运行时“偷偷换零件”，
装饰器更像是在出厂时“加装功能模块”。

---

## 🧩 用 Decorator 模拟 Monkey Patching（安全方式）

假设你想在程序运行时动态地改变某个类的方法，
我们可以用装饰器**动态注入功能**，而不是直接替换。

### 示例：为第三方类添加日志功能

```python
class Dog:
    def bark(self):
        return "woof"

# 定义一个装饰器，用于包装类方法
def add_logging(method):
    def wrapper(*args, **kwargs):
        print(f"[LOG] 调用 {method.__name__}")
        return method(*args, **kwargs)
    return wrapper

# 使用装饰器安全地“注入”新行为
Dog.bark = add_logging(Dog.bark)

d = Dog()
print(d.bark())
```

输出：

```
[LOG] 调用 bark
woof
```

这实际上**改变了方法的行为**，但方式更可控：

* 我们保留了原方法逻辑；
* 装饰器清晰地表达了“这是一次增强”；
* 如果需要，还可以轻松撤销或替换。

---

## 💡 总结

* **Monkey Patching** 是强力工具，但像手术刀一样，必须小心使用。
* **Decorator** 提供了更可读、更可维护的方式去扩展行为。
* 在绝大多数情况下，**用装饰器替代猴子补丁** 是更好的选择。

装饰器是显式设计的扩展机制，而猴子补丁是应急时的黑魔法, 在单元测试时会用到， 生产环境代码要尤其注意。

