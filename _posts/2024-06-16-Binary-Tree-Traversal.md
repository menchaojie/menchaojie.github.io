---
layout: post
title: 二叉树的遍历
categories: [data struct, binary tree]
description: 二叉树遍历，前中后序，层序
keywords: 二叉树，遍历
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

多种二叉树排序的算法

## 前中后三序

二叉树的前中后序分别指：

前序：“根->左->右”，[leetcode](https://leetcode.cn/problems/binary-tree-preorder-traversal/description/)链接

中序：“左->根->右”，[leetcode](https://leetcode.cn/problems/binary-tree-inorder-traversal/submissions/540486845/)链接

后序：“左->右->根”，[leetcode](https://leetcode.cn/problems/binary-tree-postorder-traversal/submissions/540488098/)链接

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

#### 中序

```cpp
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int>ans;
        if(!root)return ans;
        stack<TreeNode* > s;
        TreeNode *p=root;
        while(p||!s.empty()){
            if(p){
                s.push(p);
                p=p->left;
            }else{
                p=s.top();
                s.pop();
                ans.push_back(p->val);
                p=p->right;
            }
        }
        return ans;
    }
};
```

#### 后续

```cpp
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int>ans;
        if(!root)return ans;
        stack<TreeNode*> s;
        TreeNode *p=root;
        TreeNode *pre=nullptr; //recode the pre visited node
        while(p||!s.empty()){
            if(p){
                s.push(p);
                p=p->left;
            }else if(!s.empty()){
                p=s.top();
                //left child is null
                if( p->right && p->right!=pre){//right non-empty and non-visited
                    p=p->right;
                }else{//right is null, or visited, need to visite p
                    s.pop();
                    ans.push_back(p->val);
                    pre=p;
                    p=nullptr; //prevant duplicate s.push(), and find the suitable root
                }
            }
        }
        return ans;
    }
};
```

##  层序

层序的方法也有两种：

- 递归：使用depth辅助标记层数，递归调用孩子结点时，depth+1
- 迭代：使用队列+层内循环，每次将上一层全部出队，下一层全部入队

常见算法题目：
- 自上而下层序：[leetcode](https://leetcode.cn/problems/binary-tree-level-order-traversal/submissions/541161967/)链接 
- 自下而上层序：[leetcode](https://leetcode.cn/problems/binary-tree-level-order-traversal-ii/solutions/402560/er-cha-shu-de-ceng-ci-bian-li-ii-by-leetcode-solut/)链接

其中，自下而上的遍历用到了c++中的`reverse()`函数：
```cpp
//头文件
#include <algorithm>
//使用方法
reverse(a, a+n);//翻转数组，n为数组中的元素个数
reverse(str.begin(), str.end());//翻转字符串
reverse(vec.begin(), vec.end());//翻转向量
```



###  递归
```cpp
class Solution {
public:
    void preOrder(TreeNode*root, vector<vector<int>>&ans, int depth=0){
        if(!root)return;
        //initialize the row <=> level
        if(ans.size()==depth)ans.push_back(vector<int>());
        ans[depth].push_back(root->val);
        preOrder(root->left, ans, depth+1);
        preOrder(root->right, ans, depth+1);
    }
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> ans;
        preOrder(root, ans, 0);
        return ans;
    }
};
```

###  迭代

```cpp
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> ans;
        if(!root)return ans;
        TreeNode *p=root;
        queue<TreeNode*> q;
        q.push(p);
        while(!q.empty()){
            vector<int> row;
            int num_level=q.size();
            for(int i=0;i<num_level;i++){
                p=q.front();
                q.pop();
                row.push_back(p->val);
                if(p->left)(q.push(p->left));
                if(p->right)(q.push(p->right));
            }
            ans.push_back(row);
        }
        return ans;
    }
};
```