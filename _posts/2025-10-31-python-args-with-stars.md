---
layout: post
title: python 中的带*的参数
categories:
  - python
description: 学一些python知识
keywords:  
  - python
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---



#  Python 中的带星号参数（*args / **kwargs）总结


## 一、`*args`：接收任意数量的位置参数

```python
def greet(*args):
    print(args)

greet('Alice', 'Bob', 'Charlie')
# 输出: ('Alice', 'Bob', 'Charlie')
````

📘 `*args` 会把所有**位置参数**打包成一个 **元组**。

示例：

```python
def add(*nums):
    return sum(nums)

print(add(1, 2, 3, 4))  # 输出 10
```

---

## 二、`**kwargs`：接收任意数量的关键字参数

```python
def show_info(**kwargs):
    print(kwargs)

show_info(name='Alice', age=20, city='Paris')
# 输出: {'name': 'Alice', 'age': 20, 'city': 'Paris'}
```

📘 `**kwargs` 会把所有**关键字参数**打包成一个 **字典**。

---

## 三、同时使用 `*args` 和 `**kwargs`

```python
def demo(a, b, *args, **kwargs):
    print("a:", a)
    print("b:", b)
    print("args:", args)
    print("kwargs:", kwargs)

demo(1, 2, 3, 4, x=10, y=20)
# 输出:
# a: 1
# b: 2
# args: (3, 4)
# kwargs: {'x': 10, 'y': 20}
```

⚙️ **参数顺序规则：**

```
位置参数 → *args → 关键字参数 → **kwargs
```

---

## 四、星号在函数调用中的“解包”作用

### 1. `*` 解包序列

```python
def add(a, b, c):
    print(a + b + c)

nums = [1, 2, 3]
add(*nums)  # 等价于 add(1, 2, 3)
```

### 2. `**` 解包字典

```python
def greet(name, age):
    print(f"{name} is {age} years old.")

person = {"name": "Alice", "age": 20}
greet(**person)  # 等价于 greet(name='Alice', age=20)
```

---

## 五、仅限关键字参数（keyword-only arguments）

在函数定义中，放一个裸的 `*`，表示**后面的参数必须用关键字方式传入**：

```python
def user_info(name, *, age, city):
    print(name, age, city)

user_info("Alice", age=18, city="Paris")  # ✅ 正确
user_info("Alice", 18, "Paris")           # ❌ 报错
```

---

## 六、仅限位置参数（positional-only arguments, Python 3.8+）

使用 `/` 来声明**前面的参数必须按位置传入**：

```python
def add(a, b, /, c):
    print(a + b + c)

add(1, 2, c=3)  # ✅
add(a=1, b=2, c=3)  # ❌ 报错
```

---

## 七、参数定义顺序总结

函数定义中的推荐顺序：

```
位置参数 → / → 默认参数 → *args → 仅限关键字参数 → **kwargs
```

---

## 八、总结对照表

| 符号         | 定义位置  | 含义            | 类型    |
| ---------- | ----- | ------------- | ----- |
| `*args`    | 函数定义  | 任意数量的位置参数     | tuple |
| `**kwargs` | 函数定义  | 任意数量的关键字参数    | dict  |
| `*`        | 函数定义中 | 后面的参数必须用关键字传入 | —     |
| `/`        | 函数定义中 | 前面的参数必须用位置传入  | —     |
| `*`        | 函数调用  | 解包序列          | —     |
| `**`       | 函数调用  | 解包字典          | —     |

---

## 九、综合示例

```python
def func(a, b=2, *args, c=3, **kwargs):
    print("a =", a)
    print("b =", b)
    print("args =", args)
    print("c =", c)
    print("kwargs =", kwargs)

func(1, 5, 6, 7, c=10, d=20, e=30)
```

输出：

```
a = 1
b = 5
args = (6, 7)
c = 10
kwargs = {'d': 20, 'e': 30}
```

---

## 🔑 一句话总结

> `*` 和 `**` 在函数定义中用来**打包参数**，在函数调用中用来**解包数据结构**。
> 它们让 Python 函数更灵活、更通用。

---