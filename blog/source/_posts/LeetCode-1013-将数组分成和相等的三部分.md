---
title: LeetCode-1013.将数组分成和相等的三部分
date: 2020-03-11 10:47:16
tags: LeetCode
categories: 题解
---

刚开始做这道题我想到的只有暴力枚举，看了评论才豁然开朗。

<!--more-->

#### [1013. 将数组分成和相等的三个部分](https://leetcode-cn.com/problems/partition-array-into-three-parts-with-equal-sum/)

> 给你一个整数数组 A，只有可以将其划分为三个和相等的非空部分时才返回 true，否则返回 false。
>
> 形式上，如果可以找出索引 i+1 < j 且满足 (A[0] + A[1] + ... + A[i] == A[i+1] + A[i+2] + ... + A[j-1] == A[j] + A[j-1] + ... + A[A.length - 1]) 就可以将数组三等分。
>
> **示例 1：**
>
> ```
> 输出：[0,2,1,-6,6,-7,9,1,2,0,1]
> 输出：true
> 解释：0 + 2 + 1 = -6 + 6 - 7 + 9 + 1 = 2 + 0 + 1
> ```
>
> **示例 2：**
>
> ```
> 输入：[0,2,1,-6,6,7,9,-1,2,0,1]
> 输出：false
> ```
>
> **示例 3：**
>
> ```
> 输入：[3,3,6,5,-2,2,5,1,-9,4]
> 输出：true
> 解释：3 + 3 = 6 = 5 - 2 + 2 + 5 + 1 - 9 + 4
> ```
>
> **提示：**
>
> 1. `3 <= A.length <= 50000`
> 2. `-10^4 <= A[i] <= 10^4`

### 暴力法

首先分析给定的数据范围`3 <= A.length <= 50000`，`-10^4 <= A[i] <= 10^4`，由此可知我们的数组的总和在`[-5e, 5e]`(e代表亿)之间，`int`的大致范围是`[-21e, 21e]`所以不用考虑累加越界的情况。

暴力枚举的想法很简单通过两根指针将数组分成三个部分，将第一部分累加起来和后面累加之和比，看是否能够将数组分成三个部分的前提下找到和相等的部分。时间复杂度为O(n<sup>2</sup>)，最后一个测试用例会超时。

```C++
class Solution {
public:
    bool canThreePartsEqualSum(vector<int>& A) {
        // 一个个枚举
        int size = A.size();
        if (size < 3)
            return false;
        int sum_p1 = 0, sum_p2 = 0, sum_p3 = 0;
        int i = 0, j = 0, k = 0;
        for (i = 0; i < size - 2; ++i){			//size - 2 确保了可以至少分成三部分
            sum_p1 += A[i];
            sum_p2 = 0, sum_p3 = 0;
            for (j = i + 1; j < size - 1; ++j){ //size - 1 确保了第三部分至少有一个元素
                sum_p2 += A[j];
                if (sum_p2 == sum_p1)
                    break;
            }
            for (k = j + 1; k < size; ++k)
                sum_p3 += A[k];
            if (sum_p1 == sum_p2 && sum_p1 == sum_p3)
                return true;  
        }
        return false;
    }
};
```



### 正常解法

先将A的所有元素累加，如果能整除`3`说明有可能分成三个累加数之和。


```C++
class Solution {
public:
    bool canThreePartsEqualSum(vector<int>& A) {
        int size = A.size();
        if (size < 3)
            return false;
        int sum = 0;
        for(auto i : A)
            sum += i;
        if(sum % 3 != 0)
            return false;
        int tmp_sum = 0, count = 0, i = 0;
        for(i = 0; i < A.size(); ++i){
            tmp_sum += A[i];
            if(tmp_sum == sum / 3){
                count++;
                tmp_sum = 0;
            }
        }
        if(count >= 3)		//防止特殊的测试用例 [10,-10,10,-10,10,-10,10,-10]
            return true;
        return false;
    }
};
```

