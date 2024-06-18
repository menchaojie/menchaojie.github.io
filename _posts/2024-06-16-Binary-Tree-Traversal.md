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

## 前中后三序

二叉树的前中后序分别指：

“根左右”，[力扣](https://leetcode.cn/problems/binary-tree-preorder-traversal/description/)链接。

“左根右”，[力扣](https://leetcode.cn/problems/binary-tree-inorder-traversal/submissions/540486845/)链接。

“左右根” [力扣](https://leetcode.cn/problems/binary-tree-postorder-traversal/submissions/540488098/)链接。

的排序方式。

主要遍历方法，分为递归的和非递归的方法

### 递归

#### 前序
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

#### 中序

```cpp
class Solution {
public:
    void inOrder(TreeNode *root, vector<int> & ans){
        if(!root)return ;
        inOrder(root->left, ans);
        ans.push_back(root->val);
        inOrder(root->right,ans);
    }
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int>ans;
        inOrder(root, ans);
        return ans;
    }
};
```

#### 后序

```cpp
class Solution {
public:
    void postOder(TreeNode*root, vector<int> & ans){
        if(!root)return;
        postOder(root->left, ans);
        postOder(root->right, ans);
        ans.push_back(root->val);
    }
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int>ans;
        postOder(root, ans);
        return ans;
    }
};
```

### 迭代(非递归)

#### 前序
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

