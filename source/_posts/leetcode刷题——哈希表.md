---
title: leetcode刷题——哈希表
date: 2020-03-17 23:28:59
tags: [leetcode]
categories: [leetcode,哈希表]
comments:
---
本文记录leetcode刷哈希表题目的记录。
<!--more-->
### 拼写单词
#### 题目描述
给你一份『词汇表』（字符串数组） words 和一张『字母表』（字符串） chars。

假如你可以用 chars 中的『字母』（字符）拼写出 words 中的某个『单词』（字符串），那么我们就认为你掌握了这个单词。

注意：每次拼写时，chars 中的每个字母都只能用一次。

返回词汇表 words 中你掌握的所有单词的 长度之和。


示例 1：
```
输入：words = ["cat","bt","hat","tree"], chars = "atach"
输出：6
解释： 
可以形成字符串 "cat" 和 "hat"，所以答案是 3 + 3 = 6。
```
示例 2：
```
输入：words = ["hello","world","leetcode"], chars = "welldonehoneyr"
输出：10
解释：
可以形成字符串 "hello" 和 "world"，所以答案是 5 + 5 = 10。
```

提示：

- 1 <= words.length <= 1000
- 1 <= words[i].length, chars.length <= 100
- 所有字符串中都仅包含小写英文字母

#### 思路
- 遍历chars和words
- 创建一个辅助数组存储26个字母对应的数量
- 已创建好的hash表不能改变，需要两个数组
- memset函数为复制0个sizoef（map）给map

#### 过程
```c
int countCharacters(char ** words, int wordsSize, char * chars){
    int nums = strlen(chars);
    if (wordsSize == 0 || nums == 0) return 0;  //一者为空直接出局
    int max = 0, j = 0;
    int map[26], count[26]; //hash表 与 计数数组
    memset(map, 0, sizeof(map)); //hash表初始化
    for (int c = 0; c < nums; c++) { //生成hash值
        map[chars[c] - 'a']++;
    }
    for (int i = 0; i < wordsSize; i++) {
        int len = strlen(words[i]);
        if (len > nums) continue;  //单个word长度超过字母表长度，无效
        memset(count, 0, sizeof(count)); //计数数组归零
        for (j = 0; j < len; j++) {
            if (map[words[i][j] - 'a'] <= count[words[i][j] - 'a']) break; //hash值为0 或 重复字母不足
            count[words[i][j] - 'a']++;  //计数+1，代表字母重复一次
        }
        if (j == len) max += len; //当上面循环有break存在时，无效
    }
    return max;
}
```
本题借鉴了`ben-xiang-lan-jing-de-fu-you`的思路
链接：https://leetcode-cn.com/problems/find-words-that-can-be-formed-by-characters/solution/cyu-yan-qing-shuang-jie-fa-ji-du-jie-jin-shuang-10/

### 最长回文串
#### 题目描述
给定一个包含大写字母和小写字母的字符串，找到通过这些字母构造成的最长的回文串。

在构造过程中，请注意区分大小写。比如 "Aa" 不能当做一个回文字符串。

注意:
假设字符串的长度不会超过 1010。

示例 1:
```
输入:
"abccccdd"

输出:
7

解释:
我们可以构造的最长的回文串是"dccaccd", 它的长度是 7。
```
#### 思路
- 主要字母大小写不同，分开存放
- 用哈希数组保证每个字母数量独立计算
- 偶数有对称性，可全部计入；奇数则只能加入偶数个数
- 奇数字母中可以加入一个作为字符串的中心
- 遍历时防止重复计算同一个字母，遍历一次后变为0

#### 过程
```c
int longestPalindrome(char * s){
    int i,j,count=0;
    int len=strlen(s);
    int map[128];
    memset(map,0,sizeof(map));

    for(i=0;i<len;i++){
        map[s[i]]++;
    }

    for(j=0;j<len;j++){
        count+=map[s[j]]/2*2;
        if(map[s[j]]%2==1&&count%2==0){
            count++;
        }
        map[s[j]]=0;
    }

    return count;

}
```

### 最小的k个数
#### 题目描述
输入整数数组 arr ，找出其中最小的 k 个数。例如，输入4、5、1、6、2、7、3、8这8个数字，则最小的4个数字是1、2、3、4。

示例 1：
```
输入：arr = [3,2,1], k = 2
输出：[1,2] 或者 [2,1]
示例 2：

输入：arr = [0,1,2,1], k = 1
输出：[0]
 ```
```
限制：

0 <= k <= arr.length <= 10000
0 <= arr[i] <= 10000
```
#### 思路
- 用哈希表统计每个数字的个数
- 用一个数组存储输出的数
- 输出k个数可是同一个数
- 用for循环会导致同样的数只输出一个

#### 过程
```c
int* getLeastNumbers(int* arr, int arrSize, int k, int* returnSize){
    int i,j=0,count=0;
    int map[10001]={0};
    *returnSize=k;
    int *a=(int*)malloc(sizeof(int)*k);

    for(i=0;i<arrSize;i++){
        map[arr[i]]++;
    }

    while(k!=0||j>=10001){
        if(map[j]!=0){
            a[count++]=j;
            k--;
            map[j]--;
        }else j++;
    }
    return a;
}
```



