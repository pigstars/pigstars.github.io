---
layout: post
title: "重读数据结构之二叉树"
description: "description"
category: "编程"
tags: "数据结构"
---
    
# 重读数据结构之二叉树

二叉树作为常用的数据结构。

* 二叉树：包含两个字节点（可以为空）的树
* 完全二叉树： 第i+1层有2^i个节点，除了叶结点外，其他所有节点均有两个子节点
* 二叉查找树：对于树中每个节点n，左子树中的值小于节点n的值，右子树中的值大于节点n的值v
* AVL树：每个节点左右子树高度差最大为1
* 堆：每个节点的值大于等于其每个子节点的值完全平衡，最后一层都处于最左侧位置。
* treap树：将二叉查找树以及堆结合到一种数据结构


## 二叉树遍历：
二叉树遍历分为三种
V-访问节点 L-遍历左子树 R-遍历右子树
VLR 前序树遍历 LVR 中序树遍历 LRV 后序树遍历

### 广度优先遍历：

```C
template<class T>
void BST<T>::bfs(BSTNode<T> *root)
{
	if(root == nullptr)
		return ;
	queue<BSTNode <T> *> que;
	que.push(root);
	while (!que.empty())
	{
		BSTNode <T> * temp = que.front();
		que.pop();
		visit(temp);
		if(temp -> left != nullptr)
		{
			que.push(temp -> left);
		}
		if(temp -> right != nullptr)
		{
			que.push(temp -> right);
		}        
	}
}
```

若要每行输出之后换行

```C
tempalte<class T>
void BST<T>::bfs(BSTNode<T> * root)
{
    if(root == nullptr)
			return ;
		queue<BSTNode <T> *> que;
		BSTNode<T> * last,*nlast;
		last = nlast = root ;
		que.push(root);
		while(!que.empty())
		{
			BSTNode<T> * temp = que.front();
			que.pop();
			visit(temp);
			if(temp -> left)
				que.push(temp -> left);
			if(temp -> right)
				que.push(temp -> right);
			nlast = que.back();
			if(temp == last)
			{
				last = nlast;
				cout<<endl;
			}
			else
			{
				cout<<" ";
			}
		}
}
```

### 递归实现：

```C
// 中序遍历
template<class T>
void BST<T>::inorder(BSTNode<T> *p)
{
    if(p != 0)
    {
        inorder(p->left);
        visit(p);
        inorder(p->right);
    }
}

// 先序遍历
void BST<T>::preorder(BSTNode<T> *p)
{
    if(p != 0)
    {
    visit(p);
    preorder(p->left);
    preorder(p->right);
}

// 后序遍历
void BST<T>::postorder(BSTNode<T> *p)
{
    if(p != 0)
    {
    postorder(p->left);
    postorder(p->right);
    visit(p);
    }
}

```

## 非递归实现

```C
// 先序遍历
template<class T>
void BST<T>::preorder(BSTNode<T> *root)
{
   if(root == nullptr)
			return ;
		BSTNode<T> *p = root;
		stack<BSTNode <T> *> stk;
		stk.push(root);
		while (!stk.empty())
		{
			BSTNode<T> *temp = stk.top();
			stk.pop();
			visit(temp);
			if(temp -> right != nullptr)
				stk.push(temp -> right);
			if(temp -> left != nullptr)
				stk.push(temp -> left);
		}
}

// 中序遍历
template<class T>
void BST<T>::inorder<BSTNode<T> * root>
{
      stack<BSTNode <T>*> stk;
		BSTNode <T> * p = root;
		while (!stk.empty() || p!= nullptr)
		{
			while (p)
			{
				stk.push(p);
				p = p -> left;
			}
			
			if(p == nullptr)
			{
				p = stk.top();
				stk.pop();
				visit(p);		
				p = p -> right;
			}
	   }
}

// 后序遍历
template <class T>
void BST<T>::postorder(BST<T> * root)
{
    
}
```


