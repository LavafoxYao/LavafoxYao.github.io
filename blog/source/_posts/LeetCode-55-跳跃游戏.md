---
title: LeetCode 55.跳跃游戏
date: 2020-07-15 00:14:22
tags: ['贪心', 'LeetCode']
categories: 题解
---

#### [55. 跳跃游戏](https://leetcode-cn.com/problems/jump-game/)

<!--more-->

> 给定一个非负整数数组，你最初位于数组的第一个位置。
>
> 数组中的每个元素代表你在该位置可以跳跃的最大长度。
>
> 判断你是否能够到达最后一个位置。
>
> **示例 1:**
>
> ```
> 输入: [2,3,1,1,4]
> 输出: true
> 解释: 我们可以先跳 1 步，从位置 0 到达 位置 1, 然后再从位置 1 跳 3 步到达最后一个位置。
> ```
>
> **示例 2:**
>
> ```
> 输入: [3,2,1,0,4]
> 输出: false
> 解释: 无论怎样，你总会到达索引为 3 的位置。但该位置的最大跳跃长度是 0 ， 所以你永远不可能到达最后一个位置。
> ```

思路也是和`45题 跳跃游戏` 思路一样

```C++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int pos_max = 0;
        int cur_max = 0;
        for (int i = 0;i <= nums.size() - 1; ++i){
            pos_max = max(nums[i] + i, pos_max);
            if (i == cur_max)   cur_max = pos_max;
            // 当i遍历超过cur_max 说明i走的比cur_max更远
            // 故是不可能跳完的
            if (i > cur_max)    return false;		
        }
        return true;
    }
};
```

