---
layout: post
title: 重构：多态和条件逻辑
categories:
  - Coding
description: 编程中的条件逻辑
keywords: 多态，条件逻辑
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
## 条件逻辑

```js
if ... else ....
switch ...
```
条件逻辑在少量条件判断情况下比较简洁易用，但是一旦条件多了，就很杂乱；
此时使用多态更合适。

## 多态

```js
class Animal {
  speak() {
    throw new Error("This method must be overridden");
  }
}

class Dog extends Animal {
  speak() {
    return "Woof!";
  }
}

class Cat extends Animal {
  speak() {
    return "Meow!";
  }
}

function makeAnimalSpeak(animal) {
  console.log(animal.speak());
}

makeAnimalSpeak(new Dog()); // 输出 "Woof!"
makeAnimalSpeak(new Cat()); // 输出 "Meow!"
```

在这个例子中，不需要条件判断来决定 Dog 或 Cat 的行为。多态通过不同的子类实现 speak 方法，满足了条件逻辑的需求。

## 总结

学习到两个重构，或者良好代码方法：
1. replace Loop with Pipeline
2. conditional logic --> type polymorphism 