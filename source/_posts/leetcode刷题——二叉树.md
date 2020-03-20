---
title: leetcode刷题——二叉树
date: 2020-03-03 12:05:54
tags: [leetcode]
categories: [leetcode,二叉树]
comments: true
---
本文主要为二叉树解题记录，目前有 C语言 的解题思路，其他语言、方法已理解的会补充完整。
Leetcode刷题。

<!--more-->
### 相同的树
#### 题目描述
给定两个二叉树，编写一个函数来检验它们是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

示例 1:
~~~
输入:       1         1
          / \       / \
         2   3     2   3

        [1,2,3],   [1,2,3]

~~~
输出: true
示例 2:
~~~
输入:      1          1
          /           \
         2             2

        [1,2],     [1,null,2]
~~~

输出: false
示例 3:
~~~
输入:       1         1
          / \       / \
         2   1     1   2

        [1,2,1],   [1,1,2]

输出: false
~~~
二叉树结构：
~~~

Definition for a binary tree node.
struct TreeNode {
      int val;
      struct TreeNode *left;
      struct TreeNode *right;
};
 
~~~
#### 思路
**

递归：

**

- 当两棵树均为NULL时，可能已经递归完，也可能均为空，此时返回true
- 当其中一颗tree为NULL时，返回false
- 两棵树均不为空但val不同时，返回false


#### 过程
**

C语言

**
~~~c
bool isSameTree(struct TreeNode* p, struct TreeNode* q){
    if(p==NULL&&q==NULL){
        return true;
    }else if(p!=NULL&&q==NULL){
        return false;
    }else if(p==NULL&&q!=NULL){
        return false;
    }else if(p->val==q->val){
        return isSameTree(p->left,q->left)&&isSameTree(p->right,q->right);
    }else{
        return false;
    }

}
~~~
### 对称二叉树

#### 题目描述
给定一个二叉树，检查它是否是镜像对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。
~~~
    1
   / \
  2   2
 / \ / \
3  4 4  3
~~~
但是下面这个 [1,2,2,null,3,null,3] 则不是镜像对称的:
~~~
    1
   / \
  2   2
   \   \
   3    3
~~~
说明:

如果你可以运用递归和迭代两种方法解决这个问题，会很加分。

#### 思路
**

递归： 

**
- 树为空则对称
- 值相等且有左右子树
- 左子树等于右子树
#### 过程
**

C语言

**
~~~c
bool next(struct TreeNode* root_L,struct TreeNode* root_R){
    if(!root_L&&!root_R){
        return 1;
    }
    if(!root_L||!root_R){
        return 0;
    }
    if(root_L->val==root_R->val){
        return next(root_L->left,root_R->right)&&next(root_L->right,root_R->left);
    }
    return 0;
}

bool isSymmetric(struct TreeNode* root){
    if(root==NULL) return 1;
    return next(root->left,root->right);
}

~~~

### 二叉树的最大深度
#### 题目描述
给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明: 叶子节点是指没有子节点的节点。

示例：
给定二叉树 [3,9,20,null,null,15,7]，
~~~
    3
   / \
  9  20
    /  \
   15   7
~~~
返回它的最大深度 3 。
#### 思路
**

递归：

**
- 树为空时，停止
- 树某个子树为空时，不进入递归
- 高度逐层增加，返回最大的一个

#### 过程
**

C语言

**
~~~c
int maxDepth(struct TreeNode* root){
    if(root==NULL)  return 0;
    int hl=1,hr=1;
    if(root->left){
        hl+=maxDepth(root->left);
    }
    if(root->right){
        hr+=maxDepth(root->right);
    }
    if(hl>hr){
        return hl;
    }
    return hr;
}
~~~













### 先序构建二叉树，按先序、中序、后序输出

&表示引用，更改引用值会更改被引用的值；*表示指针，和原来的值独立。
```c
#include <stdio.h>
#include <stdlib.h>
#define MAX_TREE_SIZE 100

typedef struct BiTNode
{
	char data;
	struct BiTNode *lchild,*rchild;
}BiTNode,*BiTree;

void CreateBiTree(BiTree &T)
{//按先序输入二叉树结点，#表示空树 
	char ch;
	scanf("%c",&ch);
	if(ch=='#')
	{
		T=NULL;
	}else{
	T = (BiTree)malloc(sizeof(BiTNode));
	T->data=ch;
	CreateBiTree(T->lchild);
	CreateBiTree(T->rchild);
	}
}


void PreOrderTravel(BiTree T)
{//先序遍历  根左右
    if(T==NULL) return;			//结束本次循环 
    printf("%c ",T->data);		//正在遍历的结点 
    PreOrderTravel(T->lchild);	//进入左结点 
    PreOrderTravel(T->rchild);	//左结点为空进入右结点 
}
 

void InOrderTravel(BiTree T)
{//中序遍历 左根右
    if(T==NULL)return;
    InOrderTravel(T->lchild);
    printf("%c ",T->data);
    InOrderTravel(T->rchild);
}
 

void TailOrderTravel(BiTree T)
{//后序遍历 左右根
    if(T==NULL) return; 
    TailOrderTravel(T->lchild);
    TailOrderTravel(T->rchild);
    printf("%c ",T->data);
}

int main()
{
	printf("请输入二叉树先序遍历构建二叉树（#代表空）：");
	BiTree T;
	T=(BiTree)malloc(sizeof(BiTNode));
	
	CreateBiTree(T);
	
	printf("先序遍历结果：");
	PreOrderTravel(T);
	printf("\n"); 
	
	printf("中序遍历结果：");
	InOrderTravel(T);
	printf("\n"); 
	
	printf("后序遍历结果：");
	TailOrderTravel(T); 
	printf("\n"); 
}
```


### 两个顺序表差集


```
#include <stdio.h>
#include <stdlib.h>
#define LIST_INIT_SIZE 100
#define LISTINCREMENT 10
#define OVERFLOW 3

typedef struct
{
	int *elem;
	int length;
	int listsize;
}SqList;

int InitList(SqList &L)
{//初始化顺序表 
	L.elem=(int*)malloc(LIST_INIT_SIZE*sizeof(int));
	if(!L.elem) exit(OVERFLOW);
	L.length=0;
	L.listsize=LIST_INIT_SIZE;
	return 0;
}

void defference(SqList *A,SqList *B,SqList *C)
{//删除相同的元素 
	int i = 0,j = 0,k = 0;
	
	for(i = 0; i < A->length ; i++)
	{
		j = 0;
		
		while(j < B->length && B->elem [j] != A->elem [i]) //找不同的元素 
		{
			j++;
		}
		
		if(j == B->length )//说明找到不同元素 
		{
			C->elem [k] = A->elem [i];
			
			k++;	
		}
	}
	
	C->length = k;
}

void OuputList(SqList L)
{//输出元素 
	int i;
	for(i=0;i<L.length;i++)
	{
		printf("%2d",L.elem[i]);
	}
	printf("\n");
}

int main()
{
	int n,m;
	int i,j;
	SqList La,Lb,Lc;
	InitList(La);
	InitList(Lb);
	InitList(Lc);
	
	printf("请输入La长度:");
	scanf("%d",&n);
	La.length=0;
	printf("请输入La的元素：\n");
	for(i=0;i<n;i++)
	{//输入La 
		scanf("%d",&La.elem[i]);
	}
	La.length=n;
	
	printf("请输入Lb长度:");
	scanf("%d",&m);
	Lb.length=0;
	printf("请输入Lb的元素：\n");
	for(j=0;j<m;j++)
	{//输入Lb 
		scanf("%d",&Lb.elem[j]);
	}
	Lb.length=m;
	
	Lc.length=0;
	
	printf("La:");
	OuputList(La);
	
	printf("Lb:");
	OuputList(Lb);
	
	defference(&La,&Lb,&Lc);
	printf("Lc:");
	OuputList(Lc);
}
```
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200215173436545.png)