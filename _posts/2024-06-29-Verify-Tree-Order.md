---
layout: post
title: 验证是否搜素二叉树的后序遍历
categories: [Binary Tree, Order]
description: 二叉树遍历的应用
keywords: 二叉树
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 二叉树方面的验证

实现一个函数来判断整数数组 `postorder` 是否为二叉搜索树的后序遍历结果。

链接：

[leetcode](https://leetcode.cn/problems/er-cha-sou-suo-shu-de-hou-xu-bian-li-xu-lie-lcof/description/)

[牛客](https://www.nowcoder.com/practice/a861533d45854474ac791d90e447bafd?tpId=13&tqId=23289&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

注：牛客网测试数据集多了对空数据的判断

##  递归（分治）

```cpp
#include <climits>
class Solution {
public:
    bool VerifySquenceOfBST(vector<int> sequence) {
        if(sequence.size()==0)return false;
        return postOrder(sequence, 0, sequence.size()-1);
    }
private:
    bool postOrder(vector<int>&vec, int i, int j){
        if(i>=j)return true;
        int p=i;
        while(vec[p]<vec[j])p++;
        int m=p;
        while(vec[p]>vec[j])p++;
        bool verify_root = (p==j);
        bool verify_left = postOrder(vec, i, m-1);
        bool verify_right = postOrder(vec, m, j-1);
        return verify_root && verify_left && verify_right;
    }
};
```

## 迭代（单调栈）

```cpp
#include <climits>
class Solution {
public:
    bool VerifySquenceOfBST(vector<int> sequence) {
        if(sequence.size()==0)return false;
        int root=INT_MAX;
        stack<int>s;
        for(int i=sequence.size()-1;i>=0;i--){//loop for element
            if(sequence[i]>root)return false;
            //first decrease , find it's root
            while(!s.empty() && s.top()>sequence[i]){//loop for stack
                root=s.top();
                s.pop();
            }
            s.push(sequence[i]);
        }
        return true;
    }
};
```