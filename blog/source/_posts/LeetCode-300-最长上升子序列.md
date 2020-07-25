---
title: LeetCode 300.最长上升子序列
date: 2020-07-26 00:41:27
tags: ['LeetCode', 'DP']
categories: 题解
---
[300. 最长上升子序列](https://leetcode-cn.com/problems/longest-increasing-subsequence/)
<!--more-->

>  给定一个无序的整数数组，找到其中最长上升子序列的长度。 
>
> **示例:**
>
> ```
> 输入: [10,9,2,5,3,7,101,18]
> 输出: 4 
> 解释: 最长的上升子序列是 [2,3,7,101]，它的长度是 4。
> ```
>
> **说明:**
>
> - 可能会有多种最长上升子序列的组合，你只需要输出对应的长度即可。
> - 你算法的时间复杂度应该为 O(*n2*) 。



思路:  动态规划

子序列：不要求连续子序列，只要保证元素前后顺序一致即可； 

定义状态: `dp[i]`表示`nums[i]`结尾的最长上升子序列的长度

状态转移方程：`dp[i] = max(dp[j]) + 1  其中 0 < j < i 且 nums[j] < nums[i]`

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        // DP dp[i] 表示 以nums[i]结尾的当前最长上升字序列
        if (nums.empty())   return 0;
        vector<int> dp(nums.size());
        dp[0] = 1;
        for (int i = 1; i < nums.size(); ++i){
            int tmp_max = 0;
            for (int j = 0; j < i; ++j){
                if (nums[i] > nums[j])
                    tmp_max = max(tmp_max, dp[j]);
            }
            dp[i] = tmp_max + 1;
        }
        
        int ans = 0;
        for (auto i : dp)
            ans = max(ans, i);
        return ans;
    }
};
```

