---
title: LeetCode 152.乘积最大子数组
date: 2020-07-22 03:03:28
tags: ['LeetCode', 'DP']
categories: 题解
---

#### [152. 乘积最大子数组](https://leetcode-cn.com/problems/maximum-product-subarray/)

<!--more-->

> 给你一个整数数组 `nums` ，请你找出数组中乘积最大的连续子数组（该子数组中至少包含一个数字），并返回该子数组所对应的乘积。
>
> **示例 1:**
>
> ```
> 输入: [2,3,-2,4]
> 输出: 6
> 解释: 子数组 [2,3] 有最大乘积 6。
> ```
>
> **示例 2:**
>
> ```
> 输入: [-2,0,-1]
> 输出: 0
> 解释: 结果不能为 2, 因为 [-2,-1] 不是子数组。
> ```

思路: 由于数组为正数,所以可能存在`负数`,这样就不能仅仅去记录`dp_max[i - 1]`而是还要记录`dp_min[i - 1]`,因为`负负得正` 也可以使得得到的值为最大值.

状态转移方程:

`dp_max[i] = max{nums[i], nums[i]*dp_max[i - 1], nums[i]* dp_min[i - 1]}`

`dp_min[i] = min{nums[i], nums[i]*dp_min[i - 1], nums[i]* dp_min[i - 1]}`

```C++
class Solution {
public:
    int maxProduct(vector<int>& nums) {
        int size = nums.size();
        if (size == 0)  return 0;
        int dp_max = nums[0], pre_max = nums[0];
        int dp_min = nums[0], pre_min = nums[0];
        int ans = nums[0];
        for (int i = 1; i < size; ++i){
            dp_max = max(nums[i], max(nums[i]*pre_max, nums[i]*pre_min));
            dp_min = min(nums[i], min(nums[i]*pre_max, nums[i]*pre_min));
            pre_max = dp_max;
            pre_min = dp_min;
            ans = max(ans, dp_max);
        }
        return ans;
    }
};
```

