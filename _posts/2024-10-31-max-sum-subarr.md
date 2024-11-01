---
layout: post
title: 最大子数组和
categories:
  - DP
  - Kadane
description: 动态规划的方法求最大子数组和
keywords: 动态规划
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---
## 问题

最大子数组和，
8
 \[ 1 , -2, 3, -4, 7, 8, 4, 6  \]

## 求解

### current_sum: 正贡献子数组

子数组和>0，可以类比为集体为正贡献，是潜在的最大子数组的一部分，不应舍弃；

否则当舍弃，不应留恋；若一直为负，则一直舍弃；

### max_sum: 最大子数组

最大子数组和，从所有子数组和中选出；

## 编码

```python
def max_subarray_sum(arr):
	max_sum = current_sum = arr[0]
	for num in arr[1:]:
		# use max replace conditional logic
		current_sum = max(current_sum+num, num)
		max_sum = max(max_sum, current_sum)
	return max_sum
```

## 总结

1. 最好的选择是，包容：总体为正可留下，放下：负贡献应放下，重新开始
2. max(current_sum+num, num)  <==> current_sum>0?current_sum+num:num
3. kadane 最大子数组和 <==>  双 max()