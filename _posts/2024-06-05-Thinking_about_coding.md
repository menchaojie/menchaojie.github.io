---
layout: post
title: 二叉树问题
categories: 
  - codeing
  - data struct
description: some word here
keywords: keyword1, keyword2
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---


##   二叉树非递归遍历问题
两个事情入栈出栈，栈中，栈外
```cpp
TreeNode *p=root;
stack<TreeNode *p> s;
while(!p || !s.empty()){//p或栈不空<==>p代表栈外，s代表站内
	if(p){//外有节点，入栈规则，一路向左下，左为尊，宗法制，长子长孙先享继承权
		//先序遍历：入栈前遍历，traverse q, root --> left(child)
		s.push(p);
		p=p->left;//长子继承
	}else{//q指向nullptr，外无根结点长子孙，去栈中找宗亲，
		p=s.top();
		//中序遍历：出栈后遍历，逆向寻根，raverse q, left(child)-->root
		s.pop();
		p=p->right;//寻亲次子, 如有，则为新的当权者(拥有指针)root
	}
}
```

二叉树的很多算法题目基于二叉树的遍历，遍历主要有两种:

- 1. 递归的方法

- 2. 迭代的方法（借助栈） 

对部分代码记录如下

## 重构二叉树重点是分清有左子树还是右子树
两种方法
### 重构二叉树, 迭代方式, 以栈顶元素判断
```cpp

/**
* struct TreeNode {
* int val;
* struct TreeNode *left;
* struct TreeNode *right;
* TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
* };
*/
#include <future>
#include <initializer_list>
class Solution {
public:
/**
* 代码中的类名、方法名、参数名已经指定，请勿修改，直接返回方法规定的值即可
*
*
* @param preOrder int整型vector
* @param vinOrder int整型vector
* @return TreeNode类
*/
	TreeNode* reConstructBinaryTree(vector<int>& preOrder, vector<int>& vinOrder) {
	// write code here
	//一个栈，先序首节点入栈，有左连左入栈，
	//无左，一次入栈结束(先序入栈结束)，紧接着一次出栈开始(中序出栈),
	//先序和中序都是，入栈结束，点是出栈开始点，出栈结束点连接子
		if(preOrder.size()==0)return nullptr;
		auto *root = new TreeNode(preOrder[0]);
		stack <TreeNode*> s;
		s.push(root);
		TreeNode *p = s.top();
		int i=1, j=0;
		while(i<preOrder.size()){
			//s.top is not the final left, so it has another left child
			while(!s.empty()&&s.top()->val!=vinOrder[j]){
				s.top()->left=new TreeNode(preOrder[i]);
				s.push(s.top()->left);
				i++;
			}
			//reach to the left bottom, continue pop
			while (!s.empty()&&s.top()->val==vinOrder[j]) {
				p=s.top();
				s.pop();
				j++;
			}
			//the end s.top node of the pop process may have a right child
			if(i<preOrder.size()){//bottom right node is a left child of his father
				p->right=new TreeNode(preOrder[i]);
				s.push(p->right);
				i++;
			}
		}
		return root;
	}
};

```		