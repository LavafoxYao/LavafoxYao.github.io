---
title: LeetCode 34.在排序数组中查找元素的第一个和最后一个位置
date: 2020-12-01 10:26:05
tags: ['LeetCode', '二分查找']
categories: 题解
---

经典的二分查找！

<!--more-->

> 给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。
>
> 如果数组中不存在目标值 target，返回 [-1, -1]。
>
> 进阶：
>
> 你可以设计并实现时间复杂度为 O(log n) 的算法解决此问题吗？
> **示例 1：**
>
> ```
> 输入：nums = [5,7,7,8,8,10], target = 8
> 输出：[3,4]
> ```
> **示例 2：**
>
> ```
> 输入：nums = [5,7,7,8,8,10], target = 6
> 输出：[-1,-1]
> ```
>
> **示例 3：**
>
> ```
> 输入：nums = [], target = 0
> 输出：[-1,-1]
> ```
>
> 提示：
>
> - 0 <= nums.length <= 105
> - -109 <= nums[i] <= 109
> - nums 是一个非递减数组
> - -109 <= target <= 109



思路就是采用`lower_bound()`与`upper_bound()`在有序数组中查找有序序列。

##### STL

```C++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> ans(2, -1);
        if (nums.empty())   return ans;
        int l = lower_bound(nums.begin(), nums.end(), target) - nums.begin();
        int r = upper_bound(nums.begin(), nums.end(), target) - nums.begin();
        if (l >= nums.size())   return ans;    
        if (r > 0 && nums[l] == target && nums[r - 1] == target){
            ans[0] = l;
            ans[1] = r - 1;
        }
        return ans;
    }
};
```

##### 手写lower_bound与upper_bound()

```C++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> ans(2, -1);
        int l = lower_bound(nums, 0, nums.size() - 1, target);
        int r = upper_bound(nums, 0, nums.size() - 1, target);
        if (l >= nums.size())   return ans;
        if (r > 0 && nums[l] == target && nums[r - 1] == target){
            ans[0] = l;
            ans[1] = r - 1;
        }
        return ans;
    }

    int lower_bound(const vector<int>& nums, int l, int r, int target){
        if (l > r)  return -1;
        while (l + 1 < r){
            int m = l + (r - l) / 2;
            if (nums[m] >= target)
                r = m;
            else
                l = m;
        }
        if (nums[l] == target) return l;
        if (nums[r] == target) return r;
        return -1;
    }

    int upper_bound(const vector<int>& nums, int l, int r, int target){
        if (l > r)  return -1;
        while (l + 1 < r){
            int m = l + (r - l) / 2;
            if (nums[m] > target)
                r = m;
            else
                l = m;
        }
        if (nums[l] > target && target < nums[nums.size() - 1]) return l;
        if (nums[r] > target && target < nums[nums.size() - 1]) return r;
         // nums.size() - 1是下标的界, + 1是upper需要后一个元素
        else if (target == nums[nums.size() - 1])
            return nums.size() - 1 + 1; 
        return -1;
    }
};
```

