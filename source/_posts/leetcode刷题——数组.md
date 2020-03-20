---
title: leetcode刷题——数组
date: 2020-03-08 14:51:55
tags: [leetcode]
categories: [leetcode,数组]
comments: true
---
本文主要记录在leetcode刷数组类型题目的过程。
<!--more-->
### 合并排序的数组
#### 题目描述
给定两个排序后的数组 A 和 B，其中 A 的末端有足够的缓冲空间容纳 B。 编写一个方法，将 B 合并入 A 并排序。

初始化 A 和 B 的元素数量分别为 m 和 n。

示例:
~~~
输入:
A = [1,2,3,0,0,0], m = 3
B = [2,5,6],       n = 3

输出: [1,2,2,3,5,6]
~~~
#### 思路
- 从前往后，需定义第三个数组存放，否则会覆盖A
- 从后往前，先加入大的
- 可能会有一个数组先合并完，此时剩下的数字直接插入即可
#### 过程
C语言
~~~c
void merge(int* A, int ASize, int m, int* B, int BSize, int n){
    int i=m-1;
    int j=n-1;
    int x=m+n-1;

    while(i>=0&&j>=0&&x>=0){
        if(A[i]>B[j]){
            A[x--]=A[i--];
        }else{
            A[x--]=B[j--];
        }
    }
    while(i>=0&&x>=0){
        A[x--]=A[i--];
    }
    while(j>=0&&x>=0){
        A[x--]=B[j--];
    }
}
~~~

### 多数元素
#### 题目描述
给定一个大小为 n 的数组，找到其中的多数元素。多数元素是指在数组中出现次数大于 ⌊ n/2 ⌋ 的元素。

你可以假设数组是非空的，并且给定的数组总是存在多数元素。

示例 1:
```
输入: [3,2,3]
输出: 3
示例 2:
```
```
输入: [2,2,1,1,1,2,2]
输出: 2
```
#### 思路
- 摩尔投票法
- 将一个数存储起来和数组中的另一个元素比较，相同则count+1
- 当count为0表示目前所比较的每种元素数量一致

#### 过程
```
int majorityElement(int* nums, int numsSize){
    int count=1;
    int major=nums[0];
    for(int i=1;i<numsSize;i++){
        if(major==nums[i]){
            count++;
        }else{
            count--;
        }
        if(count==0){
            major=nums[i+1];
        }
    }
    return major;
}
```





