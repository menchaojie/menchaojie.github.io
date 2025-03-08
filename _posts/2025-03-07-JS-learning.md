---
layout: post
title: JS使用初步
categories:
  - JS
description: 编程语言
keywords: JS
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
# JS语法要点

## 变量

```js
// define variable by 'var'
var num = 10
// the rule of namimg
var myFriendAge = 32
```

## 数据类型

```js
var myNum = 10  // number 数值类型, 注意这里并没有说int, float 等类型
var myStr='string_or_txt'  //string
var myBool = true//falseboolean布尔类型
var myNull = null // 用于清空变量内容,表示空
var myUn //undefined容器的默认值， 这个值得思考
```

## 运算符

```js
var sum = 1 - 2 * 3 % 4
var resultStr='你好'+ '呀'
var isTrue = 2 >= 21  //关系运算
```

## 语法

与 C语言类似，但是注意for循环使用var初始化的
```js
for(var i=0; i< 10 ; i++){}
```
在JavaScript中，**语句末尾的分号（`;`）是可选的**， JavaScript引擎解析时会自动加上分号，
但在有歧义的地方要加上分号，以免自动解析出错。

## 函数及调用
示例
```js
function getSumWithCondition (start,end, fn) {
	var sum = 0
	for(var i=start; i<=end; i++){
		if(fn(i)){
		sum += i
		}
	}
	return sum
}
var result = getSumWithCondition(50, 100, function (n) {
	if(n%2!==0){
		return true
	}
	return false
})
console.log(result)
/*
这里注意函数
function (n) {
	if(n%2!==0){
		return true
	}
}
直接作为了函数getSumWithCondition
*/
```
## 数组

JS除了想c语言一样操作，还有如下用法

| 方法           | 功能             | 返回值         | 是否修改原数组 |
| ------------ | -------------- | ----------- | ------- |
| `forEach`    | 遍历数组并执行操作      | `undefined` | 否       |
| `map`        | 遍历数组并返回新数组     | 新数组         | 否       |
| `filter`<br> | 过滤数组并返回满足条件的元素 | 新数组         | 否       |
map示例
```javascript
let numbers = [1, 2, 3];
let doubled = numbers.map(num => num * 2);
console.log(doubled); // [2, 4, 6]
```

filter示例
```javascript
let numbers = [1, 2, 3];
let evens = numbers.filter(num => num % 2 === 0);
console.log(evens); // [2]
```

## 对象
无序的存储方式, 有清晰的语义
```js
var obj = {
	name:'harry',
	age: 18
}
// usage
console.log(obj)
console.log(obj.name)
console.log(obj['name'])
for (var k in obj){ 
	console.log(k, obj[k])
}
```




