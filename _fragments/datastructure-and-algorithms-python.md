---
layout: fragment
title: 数据结构和算法(Python)
tags:
  - DataStructure
  - Algorithm
  - Python
description: 刷题
keywords: 数据结构，算法
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 链表

链表在python中的表示方法为：
```python
# class ListNode:
#     def __init__(self, x):
#         self.val = x
#         self.next = None
```

### 逆序返回链表

因为只返回值，因此，设法将值保存在数组中即可
```python
class Solution:
    def printListFromTailToHead(self , listNode: ListNode) -> List[int]:
        # write code here
        res = []
        while listNode:
            res.append(listNode.val)
            listNode=listNode.next
        return res[::-1]
```

### 反转链表

返回的仍然是一个链表，因此仍然有建立链表的过程
	   p_next
         |①
None      [ ] -->  [ ] --> [ ] --> ... --> None
   |③      ②|
         ④
cur          p
口诀：三针四步，移转移移。

```python
class Solution:
    def ReverseList(self , head: ListNode) -> ListNode:
        # write code here
        cur = None
        p = head
        p_next = head
        while(p_next):
            p_next = p.next
            p.next = cur
            cur = p
            p = p_next
        return cur
```

### 合并两个有序的链表

			[ ] -->  [ ] --> [ ] --> ... --> None
			[ ] -->  [ ] --> [ ] --> ... --> None
	cur
[ ] -->[ ] -->
ans

两表取头，交替成链，一结点，一指针，一循环，一判断
```python
class Solution:
    def Merge(self , pHead1: ListNode, pHead2: ListNode) -> ListNode:
        # write code here
        ans = ListNode(1001)
        cur = ans
        while(pHead1 and pHead2):
            if(pHead1.val < pHead2.val):
                cur.next = pHead1
                pHead1 = pHead1.next
            else:
                cur.next = pHead2
                pHead2 = pHead2.next
            cur = cur.next
        if(pHead1):
            cur.next = pHead1
        else:
            cur.next = pHead2

        return ans.next
```

## 双链表第一个交叉点

```python
class Solution:
    def FindFirstCommonNode(self , pHead1 , pHead2 ):
        # write code here
        p1 = pHead1
        p2 = pHead2
        while(p1 is not p2):
            p1 = p1.next if p1 else pHead2
            p2 = p2.next if p2 else pHead1
        return p1
```