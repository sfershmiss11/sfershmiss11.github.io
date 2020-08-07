---
title: leetcode刷题——链表
date: 2020-03-23 11:18:37
tags: [leetcode]
categories: [leetcode,链表]
comments: true
---
本文主要为Leetcode刷题中的链表题目记录。

<!--more-->

### 链表的中间结点

#### 题目描述
给定一个带有头结点 head 的非空单链表，返回链表的中间结点。

如果有两个中间结点，则返回第二个中间结点。

 

示例 1：
```
输入：[1,2,3,4,5]
输出：此列表中的结点 3 (序列化形式：[3,4,5])
返回的结点值为 3 。 (测评系统对该结点序列化表述是 [3,4,5])。
注意，我们返回了一个 ListNode 类型的对象 ans，这样：
ans.val = 3, ans.next.val = 4, ans.next.next.val = 5, 以及 ans.next.next.next = NULL.
```
示例 2：
```
输入：[1,2,3,4,5,6]
输出：此列表中的结点 4 (序列化形式：[4,5,6])
由于该列表有两个中间结点，值分别为 3 和 4，我们返回第二个结点。
```

提示：

给定链表的结点数介于 1 和 100 之间。

#### 过程
双指针
- 双指针middle指针走一格，fast指针走两格
- fast或fast->next为空时middle为中间值

暴力
- 遍历获得链表长度
- 取中间值

#### 思路
双指针法
```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */


struct ListNode* middleNode(struct ListNode* head){
    struct ListNode *middle=head,*fast=head;
    while(middle->next&&fast&&fast->next){
        middle=middle->next;
        fast=fast->next->next;
    }
    return middle;
}
```
暴力破解法
```c
struct ListNode* middleNode(struct ListNode* head){
    struct ListNode *middle=head;
    int length=0;
    while(middle!=0){
        middle=middle->next;
        length++;
    }
    middle=head;
    length=length/2;
    while(length--){
        middle=middle->next;
    }
    return middle;
}
```