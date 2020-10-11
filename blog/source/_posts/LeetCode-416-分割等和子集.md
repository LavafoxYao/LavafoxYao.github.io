---
title: LeetCode 416.分割等和子集
date: 2020-10-11 09:46:59
tags: ['LeetCode', 'DP', 'DFS']
categories: '题解'
---

`2020/10/11`的每日一题

两种方法: 1. `DP`    2. `DFS`

<!--more-->

#### [416. 分割等和子集](https://leetcode-cn.com/problems/partition-equal-subset-sum/)

> 给定一个只包含正整数的非空数组。是否可以将这个数组分割成两个子集，使得两个子集的元素和相等。
>
> 注意:
>
> 1. 每个数组中的元素不会超过 100
> 2. 数组的大小不会超过 200
>
> **示例 1:** 
>
> ```
> 输入: [1, 5, 11, 5]
> 
> 输出: true
> 
> 解释: 数组可以分割成 [1, 5, 5] 和 [11].
> ```
> **示例 2:**
>
> ```
> 输入: [1, 2, 3, 5]
> 
> 输出: false
> 
> 解释: 数组不能分割成两个元素和相等的子集.
> ```
>
> 

### 方法1 DP

**思路**：准确来说是一道`0-1`背包问题，从`nums`中挑选`（0， nums.size()）`个数，并且背包的大小为`sum/2`。

具体的思路会在代码中解释。

```C++ 
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        if (nums.empty())   return false;
        int sum = 0, size = nums.size();
        for (auto n : nums) sum += n;
        // 如果sum不能整除以2说明数组之和不能平分
        if (sum % 2)    return false;
        int half_sum = sum / 2;
        // dp[i][j] 表示从i个数字中挑选，能不能拼凑成j的大小
        // dp[i][j] 取决于 i - 1状态上的 j 和 j - nums[i - 1]的状态
        // 两者其中有一个可以拼成功则dp[i][j]就可以拼成功
        // dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i - 1]]
        vector<vector<bool>> dp(size + 1, vector<bool>(half_sum + 1, false));
        dp[0][0] = true;
        for (int i = 1; i <= size; ++i){
            for (int j = 0; j <= half_sum; ++j){
                dp[i][j] = dp[i - 1][j];
                if (j >= nums[i - 1])
                    dp[i][j] = dp[i - 1][j] || dp[i - 1][j - nums[i - 1]];
            }
        }
        return dp[size][half_sum];
    }
};
```



 ### 方法2 DFS

因为是从一个集合中挑选`(0, nums.size())`个数组组成一个`target`自然而然可以想到用暴力的`dfs`.

**提示** ：可能`LeetCode`变更了数据集，导致现在的dfs方法过不了。

```C++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        if (nums.empty())   return false;
        int sum = 0, size = nums.size();
        for (auto n : nums) sum += n;
        if (sum % 2)    return false;
        int target = sum / 2;
        sort(nums.begin(), nums.end());
        return dfs(nums, 0, target);
    }
    bool dfs(const vector<int>& nums, int s, int target){
        if (target == 0)    return true;
        if (s < 0 || s >= nums.size() || target < 0) return false;
        if (dfs(nums, s + 1, target - nums[s]))  return true;
        // 如果 return false 说明可能拼不出来 有可能是因为过度选择重复数字造成
        // 如 [1,7,7,7,9,20] ==> half_sum = 29 过度选择7是不可能拼凑出来的
        int idx = s + 1;
        while (idx < nums.size() && nums[idx] == nums[s])
            idx++;
        // 这里的target没变是因为换了个起始的位置，当前并没有选中！！！（难点）
        return dfs(nums, idx, target);
    }
};
```

