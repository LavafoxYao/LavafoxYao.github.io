---
title: LeetCode 287.寻找重复数
date: 2020-07-23 23:44:14
tags: ['LeetCode', '数组']
categories: 题解
---

#### [287. 寻找重复数](https://leetcode-cn.com/problems/find-the-duplicate-number/)

<!--more-->

> 给定一个包含 n + 1 个整数的数组 nums，其数字都在 1 到 n 之间（包括 1 和 n），可知至少存在一个重复的整数。假设只有一个重复的整数，找出这个重复的数。
>
> **示例 1:**
>
> ```
> 输入: [1,3,4,2,2]
> 输出: 2
> ```
>
> **示例 2:**
>
> ```
> 输入: [3,1,3,4,2]
> 输出: 3
> ```
>
> 说明：
>
> 1. 不能更改原数组（假设数组是只读的）。
> 2. 只能使用额外的 O(1) 的空间。
> 3. 时间复杂度小于 O(n^2) 。
> 4. 数组中只有一个重复的数字，但它可能不止重复出现一次

思路: 数组长度为`n + 1`但是其内所有的元素的值都是`1 - - n ` , 这两点可以转化成环状链表找环的起点问题

因为元素所有的值是`1--n`天然不越界, 又由于重复元素占坑所以重复元素必然为环的起点.

```C++
class Solution {
public:
    int findDuplicate(vector<int>& nums) {
        if (nums.size() == 0)   return 0;
        int fast = nums[nums[0]], slow = nums[0];
        while (fast != slow){
            fast = nums[nums[fast]];
            slow = nums[slow];
        }
        slow = 0;
        while (fast != slow){
            fast = nums[fast];
            slow = nums[slow];
        }
        return slow;
    }
};
```



