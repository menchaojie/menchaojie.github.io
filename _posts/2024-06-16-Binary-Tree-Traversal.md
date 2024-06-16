---
layout: post
title: 二叉树的遍历
categories: [数据结构,二叉树]
description: 二叉树遍历，前中后序，层序
keywords: 二叉树，遍历
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

主要的二叉树遍历方法，分为递归的和非递归的方法
## 前序
前序即为“根左右"的访问顺序。

[力扣](https://leetcode.cn/problems/binary-tree-preorder-traversal/description/)链接。
### 递归
核心代码
```cpp
class Solution {
public:
    void preOder(TreeNode *r, vector<int>&ans){
        if(r==nullptr)return;
        ans.push_back(r->val);
        preOder(r->left, ans);
        preOder(r->right, ans);
    }
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> ans;
        preOder(root, ans);
        return ans;
    }
};
```
### 迭代

```cpp
class Solution {
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> ans;
        if(!root) return ans;
        stack<TreeNode *> s;
        TreeNode *p=root;
        while(p||!s.empty()){
            if(p){
                s.push(p);
                ans.push_back(p->val);
                p=p->left;
            }else{
                p=s.top();
                s.pop();
                p=p->right;//find the non-empty right chid
            }
        }
        return ans;
    }
};
```

## 中序

## 后序