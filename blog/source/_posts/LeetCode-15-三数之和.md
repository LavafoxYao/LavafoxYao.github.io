---
title: LeetCode 15.三数之和
date: 2020-09-27 09:51:23
tags: ['LeetCode', '双指针']
categories: ['题解']
---

从开始系统学习算法到现在做题大概过了七个月了，今天再次做三数之和这道经典题目又感慨颇多。

里面用到了`回溯`中常用的去重的方法以及`双指针`.

<!--more-->

#### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

> 给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。
>
> 注意：答案中不可以包含重复的三元组。
>
>  **示例：** 
>
> ```
> 给定数组 nums = [-1, 0, 1, 2, -1, -4]，
> 
> 满足要求的三元组集合为：
> [
>   [-1, 0, 1],
>   [-1, -1, 2]
> ]
> ```

```C++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        int size = nums.size();
        if (size < 3)   return {};
        vector<vector<int>> ans;
        sort(nums.begin(), nums.end()); //排序
        for (int i = 0; i < size - 2; ++i){
            // 说明目标值是重复的 需要跳过
            if (i != 0 && nums[i - 1] == nums[i])   continue;
            // a + b + c = 0 ==> -a = b + c
            int target = 0 - nums[i];
            int l = i + 1, r = size - 1;
            while (l < r){
                int sum = nums[l] + nums[r];
                if (target == sum){
                    ans.push_back({nums[i], nums[l], nums[r]});
                    // 去除重复的l
                    l++;
                    while (l < r && nums[l - 1] == nums[l]) l++;
                    // 去除重复的r
                    r--;
                    while (l < r && nums[r + 1] == nums[r]) r--;
                }
                else if (target < sum)  r--;
                else    l++;
            }
        }
        return ans;
    }
};
```