---
title: LeetCode 1027.最长等差数列
date: 2020-10-08 10:32:23
tags: ['LeetCode', 'DP']
categories: 题解
---

坚持做题差不多有半年的时间了，但是这个学习的过程中，`DP`是我算法学习中的一道大坎，最近可能会多多练习`DP`题目，相应的`blog`也会更很多关于`DP`题目的解析。

最长等差数列，虽然做的人较少，但是确实是一道非常经典的`DP`题。

<!--more-->

#### [1027. 最长等差数列](https://leetcode-cn.com/problems/longest-arithmetic-subsequence/)

> 给定一个整数数组 A，返回 A 中最长等差子序列的长度。
>
> 回想一下，A 的子序列是列表 A[i_1], A[i_2], ..., A[i_k] 其中 0 <= i_1 < i_2 < ... < i_k <= A.length - 1。并且如果 B[i+1] - B[i]( 0 <= i < B.length - 1) 的值都相同，那么序列 B 是等差的。
>
>   **示例 1：** 
>
> ```
> 输入：[3,6,9,12]
> 输出：4
> 解释： 
> 整个数组是公差为 3 的等差数列。
> ```
>
>  **示例 2：** 
>
> ```
> 输入：[9,4,7,2,10]
> 输出：3
> 解释：
> 最长的等差子序列是 [4,7,10]。
> ```
>
>  **示例 3：** 
>
> ```
> 输入：[20,1,15,3,10,5,8]
> 输出：4
> 解释：
> 最长的等差子序列是 [20,15,10,5]。
> ```
>
> **提示：**
>
> 1. `2 <= A.length <= 2000`
> 2. `0 <= A[i] <= 10000`

**思路：**等差数列中任取连续的三个数A[i], A[j], A[k] 其中 i < j < k，有A[k] - A[j] = A[j] - A[i]

==> 2 * A[j]  = A[k] - A[i]  可以通过较大的两个数找最小的A[i]，这样就转化成了一个子问题。

关于这题特殊的地方也就是`0 <= A[i] <= 10000`，数据规模够小可以开`array`去做`hashtable`。

```C++
class Solution {
public:
    int longestArithSeqLength(vector<int>& A) {
        if (A.empty())  return 0;
        int size = A.size();
        // dp[i][j] 表示以 A[i] A[j] 顺序结尾的等差数列的长度所以初始化为2
        // dp[i][j] = dp[][i] + 1
        vector<vector<int>> dp(size, vector<int>(size, 2));
        // 由于 0 <= A[i] <= 10000  根据 a[k] * 2 最大值取到 20000
        // 所以开一个vector作为hashtable, 
        // 记录已经出现过的A[i]的下标
        vector<int> hash_table(10000*2 + 1, -1);
        int ans = 2;
        for (int i = 0; i < size; ++i){
            for (int j = i + 1; j < size; ++j){
                int start = A[i] * 2 - A[j];   
                if (start < 0 || hash_table[start] == -1) continue;
                dp[i][j] = dp[hash_table[start]][i] + 1;
                ans = max(ans, dp[i][j]);
            }
            hash_table[A[i]] = i;
        }
        return ans;
    }
};
```

