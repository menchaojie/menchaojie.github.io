---
layout: post
title: 判断是否平衡二叉树
categories:
  - data struct
  - binary tree 
  - balance binary tree 
description: 二叉树高度
keywords: 平衡二叉树
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 二叉搜索树转变为双向链表

[牛客](https://www.nowcoder.com/practice/8b3b95850edb4115918ecebdf1b4d222?tpId=13&tqId=23250&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

输入一棵节点数为 n 二叉树，判断该二叉树是否是平衡二叉树。

在这里，我们只需要考虑其平衡性，不需要考虑其是不是排序二叉树

**平衡二叉树**（Balanced Binary Tree），具有以下性质：它是一棵空树或它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树。

链接：

[leetcode](https://leetcode.cn/problems/ping-heng-er-cha-shu-lcof/description/)

```cpp
class Solution {
  public:
    bool isBalance = true;
    bool IsBalanced_Solution(TreeNode* pRoot) {
        // write code here
        if (!pRoot)return true;
        inOrderDepth(pRoot, isBalance);
        return isBalance;
    }
  private:
    int inOrderDepth(TreeNode* root, bool& isBalance) {
        if (!root)return 0;
        int leftDepth = inOrderDepth(root->left,  isBalance);
        int rightDepth = inOrderDepth(root->right, isBalance);
        if (abs(leftDepth - rightDepth) > 1) isBalance = false;
        return max(leftDepth, rightDepth) + 1;
    }
};
```

## 递归问题思考

1. 递归函数的返回值，这个在树的操作中，不同字数应该有不同的返回结果
2. 递归函数的参数值，这个可以使应用传递的方式改变函数外的值，比如成员变量，参数可以不止一个
