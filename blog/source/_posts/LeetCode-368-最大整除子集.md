---
title: LeetCode 368.最大整除子集
date: 2020-10-08 11:21:35
tags: ['LeetCode', 'DP']
categories: 题解
---

如果这题只是求最长整除子集的长度就很直白的DP，但是这题要求的是子集。

这也是这题的难点所在。

<!--more-->

#### [368. 最大整除子集](https://leetcode-cn.com/problems/largest-divisible-subset/)

> 给出一个由无重复的正整数组成的集合，找出其中最大的整除子集，子集中任意一对 (Si，Sj) 都要满足：Si % Sj = 0 或 Sj % Si = 0。
>
> 如果有多个目标子集，返回其中任何一个均可。
>
> **示例 1:**
>
> ```
> 输入: [1,2,3]
> 输出: [1,2] (当然, [1,3] 也正确)
> ```
>
>  **示例 2:** 
>
> ```
> 输入: [1,2,4,8]
> 输出: [1,2,4,8]
> ```
>
> 

**思路** ： 这题的难点是怎么回溯找到整个最长的子集，下面用的是一个`pre`数据去存储在`nums[i]`之前的下标`j`  ===> `[nums[j]  nums[i]]` 
```C++
class Solution {
public:
    vector<int> largestDivisibleSubset(vector<int>& nums) {
        if (nums.empty())   return {};
        // 先将集合排序
        sort(nums.begin(), nums.end());
        int size = nums.size();
        // dp[i] ==> 表示以nums[i]结尾的最长整除子集的长度
        // dp[i] = max{dp[j]} + 1;
        vector<int> dp(size, 1);
        // 用于记录最长整除子集的长度的前一个数字，便于回溯找到整个集合
        vector<int> pre(size, -1);
        int max_len = 0;	// 最长子集的长度
        int end_pos = 0;    // 最长子集的最后一个元素的下标
        for (int i = 0; i < size ; ++i){
            for (int j = 0; j < i; ++j){
                if (nums[i] % nums[j] == 0 && dp[i] < dp[j] + 1){
                    dp[i] = dp[j] + 1;
                    pre[i] = j;
                }
            }
            if (max_len < dp[i]){
                max_len = dp[i];
                end_pos = i;
            }
        }
        vector<int> ans;
        int idx = end_pos;
        while (idx != -1){
            ans.push_back(nums[idx]);
            idx = pre[idx];
        }
        return ans;
    }
};
```

