---
layout: post
title: 序列化以及反序列化二叉树
categories:
  - data struct
  - binary tree 
  - serialize
description: 二叉树序列化
keywords: 
  - 二叉树
  - 序列化
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

##  二叉树的序列化以及反序列化

[牛客](https://www.nowcoder.com/practice/cf7e25aa97c04cc1a68c8f040e71fb84?tpId=13&tqId=23455&ru=%2Fpractice%2F445c44d982d04483b04a54f298796288&qru=%2Fta%2Fcoding-interviews%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

请实现两个函数，分别用来序列化和反序列化二叉树，不对序列化之后的字符串进行约束，但要求能够根据序列化之后的字符串重新构造出一棵与原二叉树相同的树。

二叉树的序列化(Serialize)是指：把一棵二叉树按照某种遍历方式的结果以某种格式保存为字符串，从而使得内存中建立起来的二叉树可以持久保存。序列化可以基于先序、中序、后序、层序的二叉树等遍历方式来进行修改，序列化的结果是一个字符串，序列化时通过 某种符号表示空节点（#）  
  
二叉树的反序列化(Deserialize)是指：根据某种遍历顺序得到的序列化字符串结果str，重构二叉树。


链接：

[leetcode](https://leetcode.cn/problems/h54YBf/solutions/1038204/xu-lie-hua-yu-fan-xu-lie-hua-er-cha-shu-dh5vl/)

## 代码示例

```cpp
#include <string>
class Solution {
  public:
    void preOrder(TreeNode* root, string& str) {
        if (!root) {
            str += "#,";
            return;
        }
        str += to_string(root->val) + ",";
        preOrder(root->left, str);
        preOrder(root->right, str);
    }
    char* Serialize(TreeNode* root) {
        string ans;
        preOrder(root, ans);
        char* charAns =  new char[ans.length() + 1];
        strcpy(charAns, ans.c_str());
        charAns[ans.length()] = '\0';
        return charAns;
    }
    queue<string> getStrQ(char* s) {
        queue<string> strQ;
        string str;
        char* p = s;
        while (*p) {
            if (*p != ',') {
                str += *p;
            } else {
                strQ.push(str);
                str.clear();
            }
            p++;
        }
        return strQ;
    }
    TreeNode* preOrderDes(queue<string>& strQ) {
        if (strQ.front() == "#") {
            strQ.pop();
            return nullptr;
        }
        auto* root = new TreeNode(stoi(strQ.front()));
        strQ.pop();
        root->left = preOrderDes(strQ);
        root->right = preOrderDes(strQ);
        return root;
    }
    TreeNode* Deserialize(char* str) {
        queue<string> strQ = getStrQ(str);
        TreeNode* ans = preOrderDes(strQ);
        return ans;
    }
};
```

## 问题思考

waiting...
