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

C
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

C
```c
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

### 一维数组的动态和

#### 题目描述

给你一个数组 nums 。数组「动态和」的计算公式为：runningSum[i] = sum(nums[0]…nums[i]) 。

请返回 nums 的动态和。

 

示例 1：

```
输入：nums = [1,2,3,4]
输出：[1,3,6,10]
解释：动态和计算过程为 [1, 1+2, 1+2+3, 1+2+3+4] 。
```

示例 2：

```
输入：nums = [1,1,1,1,1]
输出：[1,2,3,4,5]
解释：动态和计算过程为 [1, 1+1, 1+1+1, 1+1+1+1, 1+1+1+1+1] 。
```

示例 3：

```
输入：nums = [3,1,2,10,1]
输出：[3,4,6,16,17]
```

提示：

```
1 <= nums.length <= 1000
-10^6 <= nums[i] <= 10^6
```

#### 思路

- 为了不改变原数组，增加了新数组arr
- 也可以直接使用原数组

#### 过程

C
```c
int* runningSum(int* nums, int numsSize, int* returnSize){
    int* arr;
    arr = (int*)malloc(sizeof(int)*numsSize);

    arr[0]=nums[0];
    for(int i = 1;i < numsSize;i ++){
        arr[i] = arr[i-1] + nums[i];
    }

    *returnSize = numsSize;
    return arr;
}
```

### 拥有最多糖果的孩子

#### 题目描述

给你一个数组 candies 和一个整数 extraCandies ，其中 candies[i] 代表第 i 个孩子拥有的糖果数目。

对每一个孩子，检查是否存在一种方案，将额外的 extraCandies 个糖果分配给孩子们之后，此孩子有 最多 的糖果。注意，允许有多个孩子同时拥有 最多 的糖果数目。

示例 1：
```
输入：candies = [2,3,5,1,3], extraCandies = 3
输出：[true,true,true,false,true] 
解释：
孩子 1 有 2 个糖果，如果他得到所有额外的糖果（3个），那么他总共有 5 个糖果，他将成为拥有最多糖果的孩子。
孩子 2 有 3 个糖果，如果他得到至少 2 个额外糖果，那么他将成为拥有最多糖果的孩子。
孩子 3 有 5 个糖果，他已经是拥有最多糖果的孩子。
孩子 4 有 1 个糖果，即使他得到所有额外的糖果，他也只有 4 个糖果，无法成为拥有糖果最多的孩子。
孩子 5 有 3 个糖果，如果他得到至少 2 个额外糖果，那么他将成为拥有最多糖果的孩子。
```

示例 2：
```
输入：candies = [4,2,1,1,2], extraCandies = 1
输出：[true,false,false,false,false] 
解释：只有 1 个额外糖果，所以不管额外糖果给谁，只有孩子 1 可以成为拥有糖果最多的孩子。
```

示例 3：
```
输入：candies = [12,1,12], extraCandies = 10
输出：[true,false,true]
```

提示：
```
2 <= candies.length <= 100
1 <= candies[i] <= 100
1 <= extraCandies <= 50
```
#### 思路

- 找出最大的数num
- 遍历数组比较大小

#### 过程
C
```c
bool* kidsWithCandies(int* candies, int candiesSize, int extraCandies, int* returnSize){
    if(candies==NULL||candiesSize<=0){
        *returnSize=0;
        return NULL;
    }
    int i;
    bool *arr=(bool *)malloc(sizeof(bool)*candiesSize);
    int num=candies[0];
    for(i=0;i<candiesSize;i++){
        if(num<candies[i]) num=candies[i];
    }
    for(i=0;i<candiesSize;i++){
        if((candies[i]+extraCandies)>=num){
            arr[i]=true;
        }else{
            arr[i]=false;
        }
    }
    *returnSize = candiesSize;
    return arr;
}
```