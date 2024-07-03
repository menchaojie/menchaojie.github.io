---
layout: post
title: 二叉搜索树转变为双向链表
categories: [Binary Tree, LinkList]
description: 二叉树Mirros遍历的应用
keywords: 二叉树
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 二叉搜索树转变为双向链表

[牛客](https://www.nowcoder.com/practice/947f6eb80d944a84850b0538bf0ec3a5?tpId=13&tqId=23253&ru=/exam/oj/ta&qru=/ta/coding-interviews/question-ranking&sourceUrl=%2Fexam%2Foj%2Fta%3Fpage%3D1%26tpId%3D13%26type%3D13)

输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。如下图所示
数据范围：输入二叉树的节点数 0≤𝑛≤10000≤n≤1000，二叉树中每个节点的值 0≤𝑣𝑎𝑙≤10000≤val≤1000  
要求：空间复杂度𝑂(1)O(1)（即在原树上操作），时间复杂度 𝑂(𝑛)O(n)

链接：

[leetcode](https://leetcode.cn/problems/er-cha-sou-suo-shu-yu-shuang-xiang-lian-biao-lcof/description/)

```cpp
class Solution {
public:
    TreeNode* Convert(TreeNode* pRootOfTree) {
		if(!pRootOfTree)return nullptr;
		auto *ans = new TreeNode(0);
		ans->right=pRootOfTree;
		TreeNode *p_pre=ans;
		TreeNode *p=ans->right;
		TreeNode *pre=nullptr;
		TreeNode *tmp=nullptr;
		while(p){
			while(p->left){//left exist, pre exist
				pre=getPreNode(p);
				if(pre->right==nullptr){
					pre->right=p;
					p=p->left;
				}else{
					break;
				}
			}
			p_pre->right=p;
			p->left=p_pre;
			p_pre=p;
			p=p->right;
		}
		ans=ans->right;
		ans->left=nullptr;
		return ans;
    }
private:
	//if root has left child,  it has preNode
	TreeNode* getPreNode(TreeNode *root){
		TreeNode *pre=root->left;
		//preNode is null, or it's->right child is root
		while (pre->right!=nullptr && pre->right != root) pre=pre->right;
		return pre;
	}
};

```

