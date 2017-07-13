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
// 使用队列来进行宽度优先遍历
// 同时使用last表示当前行的最后一个，nlast表示下一行的最后一个元素
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
			 // 每当有元素进入时，nlast指向队列最末端元素，此时nlast不一定是下一行的最末端元素
			if(temp == last)
			{
				cout<<endl;
				last = nlast; 
				// 当该行遇到最后一个元素时，换行，此时nlast一定是下一行的最末元素，因为该行的所有孩子进入队列

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
// 通过使用栈，访问该节点，该节点的右儿子先入（因为先入后出），左儿子进入栈。如此循环，当栈为空时结束。
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
// 根节点入栈
// 循环执行以下操作：如果栈顶节点的左孩子存在，左孩子入栈。如果左孩子不存在，出战并访问该节点，检查右孩子，右孩子存在，则右孩子入栈。
// 栈空时算法结束
template<class T>
void BST<T>::inorder<BSTNode<T> * root>
{
      stack<BSTNode <T>*> stk;
		BSTNode <T> * p = root;
		if(root == nullptr)
		return ;
		while (!stk.empty() || p!= nullptr)
		{
			while (p)
			{
				stk.push(p);
				p = p -> left;
			}
			if(!stk.empty())
			{
				p = stk.top();
				stk.pop();
				visit(p);		
				p = p -> right;
			}
	   }
}

// 后序遍历
// 准备两个指针，首先将最左侧的节点依次入栈，若该节点右侧为空或已被访问，方才能够访问该节点，否则将该节点再次入栈，并指向该节点右侧，重复上述循环。
template <class T>
void BST<T>::postorder(BSTNode<T> * root)
{
   BSTNode<T> * p ,* hasChecked;
		stack<BSTNode<T> *> stk;
		p = hasChecked = root;
		while(p != 0)
		{
			for(;p -> left;p = p-> left)
			{
				stk.push(p);
			}
			while(p->right == 0 || p->right == hasChecked)
			{
				visit(p);
				hasChecked = p;
				if(stk.empty())
					return ;
				p = stk.top();
				stk.pop();
			}
			stk.push(p);
			p = p->right;
		}}
		
// Morris 算法 不使用额外空间的非递归中序遍历
/*
     1          
    / \          
   2   3     
  ／\   \            
  4 5    6            
                   
  if 没有左节点，访问该节点，指向右节点
  else  
        寻找左节点的最右节点
            若该节点的右节点为空 
                则与主指针相连，主指针左移
            若该节点的右节点为主指针（表示已经连接过，左部已经全部访问过）
                则访问主指针
                主指针右移
*/
template <class T>
void BST<T>::Inorder(BSTNode <T> * root)
{
    BSTNode <T> * p,*temp;
	p = temp = root;
	while(p)
	{
		if(p -> left == nullptr)
		{
			visit(p);
			p = p -> right;
		}
		else
		{
			temp = p -> left;
			while (temp -> right != nullptr && temp -> right != p )
			{
				temp = temp -> right;
			}
			if(temp -> right == nullptr)
			{
				temp -> right = p;
				p = p -> left;
			}
			else if(temp -> right == p)
			{
				visit(p);
				temp -> right = nullptr;
				p = p -> right;
			}
		}
	}

}
```


