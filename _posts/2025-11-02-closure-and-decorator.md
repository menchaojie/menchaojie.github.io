---
layout: post
title: 闭包和装饰器
categories:
  - python
description: 学一些python知识
keywords:  
  - python
  - closure
  - decorator
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---


# python中一些关于闭包和装饰器的使用

###  历史渊源和设计演进 
#### 闭包的发展
- 起源 ：λ演算(1930s) → Lisp(1958) → 现代语言
- 思想 ：函数可以携带环境状态
- 应用 ：函数工厂、状态管理 
#### 装饰器的发展
- 起源 ：Python 2.4(2005年)引入@语法
- 思想 ：基于闭包，专门用于函数增强
- 应用 ：AOP(面向切面编程)、元编程
###  使用场景总结 
#### 使用闭包的场景 ：
1. 函数工厂 ：根据参数创建不同功能的函数
2. 状态保持 ：计数器、缓存等需要记忆状态的场景
3. 配置函数 ：根据配置创建特定行为的函数 
#### 使用装饰器的场景 ：
1. 功能增强 ：日志、计时、重试等横切关注点
2. 权限控制 ：认证、授权检查
3. 资源管理 ：数据库连接、文件操作等
4. 性能优化 ：缓存、记忆化(memoization)

### 装饰器是闭包的语法糖

```python
# 装饰器的等价写法揭示了其闭包本质

# 语法糖写法
@decorator
def my_function():
    pass

# 等价于闭包写法
def my_function():
    pass
my_function = decorator(my_function)  # 闭包应用

```

### 装饰器可以说是闭包的特殊应用

用下面的示例可以略见两者之间的关系

```python
"""
闭包 vs 装饰器 对比示例
"""

# ========== 闭包示例 ==========
def create_power_function(exponent):
    """闭包：创建幂函数"""
    def power(base):
        """计算base的exponent次方"""
        return base ** exponent
    return power

# 使用闭包
square = create_power_function(2)  # 创建平方函数
cube = create_power_function(3)    # 创建立方函数

print(f"闭包 - 平方: {square(5)}")  # 25
print(f"闭包 - 立方: {cube(5)}")    # 125

# ========== 装饰器示例 ==========
def logging_decorator(func):
    """装饰器：添加日志功能"""
    def wrapper(*args, **kwargs):
        print(f"调用函数: {func.__name__}, 参数: {args}, {kwargs}")
        result = func(*args, **kwargs)
        print(f"函数返回: {result}")
        return result
    return wrapper

# 使用装饰器
@logging_decorator
def add_numbers(a, b):
    """普通加法函数"""
    return a + b

# 调用被装饰的函数
result = add_numbers(3, 7)
print(f"装饰器结果: {result}")

# ========== 混合使用示例 ==========
def create_retry_decorator(max_attempts=3):
    """带参数的装饰器（闭包+装饰器）"""
    def decorator(func):
        """真正的装饰器"""
        def wrapper(*args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise e
                    print(f"尝试 {attempt+1} 失败，重试...")
            return None
        return wrapper
    return decorator

# 使用带参数的装饰器
@create_retry_decorator(max_attempts=2)
def unreliable_division(a, b):
    """不可靠的除法函数"""
    import random
    if random.random() < 0.7:
        raise ValueError("随机错误")
    return a / b

# 测试混合使用
try:
    result = unreliable_division(10, 2)
    print(f"混合使用结果: {result}")
except Exception as e:
    print(f"最终失败: {e}")
```

### 装饰器的调用

```python
def my_closure(addtion=4):
    print(f"A .the closure function is {my_closure.__name__}")
    y = addtion
    # print(f"the closure variable y is : {y}")
    def real_decorated(func):
        print(f"B. the real decorate function is {real_decorated.__name__}")
        def enhancer_orgin_func(*args):
            print(f"C. the wrap function for origin is: {enhancer_orgin_func.__name__}")
            x = func(*args)
            print(f"original result is: {x}, and the closure variable y is: {y}, the final result is decorated as x+y: {x + y}")
            return x + y
        return enhancer_orgin_func
    return real_decorated 

@my_closure()
def normal_func(x):
    print(f"D. the original function is: {normal_func.__name__}")
    return x**2

a = normal_func(3)
print(f"the final result is a : {a}")

```

输出为：

```bash
A .the closure function is my_closure
B. the real decorate function is real_decorated
C. the wrap function for origin is: enhancer_orgin_func
D. the original function is: enhancer_orgin_func
original result is: 9, and the closure variable y is: 4, the final result is decorated as x+y: 13
the final result is a : 13
```

### 总结

闭包和装饰器体现的是函数式编程的思想，两者关系可以概括为 ：

✅ 相同点 ：

- 都是两层函数嵌套结构
- 都利用闭包的记忆特性
- 都返回函数对象

❌ 不同点 ：

- 目的 ：闭包用于创建带状态的函数，装饰器用于增强函数行为
- 参数 ：闭包的环境参数任意，装饰器必须接收函数
- 语法 ：闭包直接调用，装饰器有@语法糖
- 抽象层次 ：装饰器是闭包的一种高级应用

关键点 ： 所有装饰器都是闭包，但并非所有闭包都是装饰器 。装饰器是专门为函数增强而设计的闭包模式。