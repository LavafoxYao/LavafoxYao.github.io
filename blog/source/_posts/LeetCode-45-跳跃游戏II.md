---
title: LeetCode 45.跳跃游戏II
date: 2020-07-15 00:00:26
tags: ['贪心', 'LeetCode']
categories: 题解
---

#### [45. 跳跃游戏 II](https://leetcode-cn.com/problems/jump-game-ii/)

<!--more-->

> 给定一个非负整数数组，你最初位于数组的第一个位置。
>
> 数组中的每个元素代表你在该位置可以跳跃的最大长度。
>
> 你的目标是使用最少的跳跃次数到达数组的最后一个位置。
>
> **示例:**
>
> ```
> 输入: [2,3,1,1,4]
> 输出: 2
> 解释: 跳到最后一个位置的最小跳跃数是 2。
>      从下标为 0 跳到下标为 1 的位置，跳 1 步，然后跳 3 步到达数组的最后一个位置。
> ```
>
> **说明:**
>
> 假设你总是可以到达数组的最后一个位置.

思路是参考:[ikaruga]( https://leetcode-cn.com/problems/jump-game-ii/solution/45-by-ikaruga/ )

特别是他提供的这张图很能说明问题:

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/07/Snipaste_2020-07-15_00-06-38.png)

思路: 遍历原数组的时候要有上图的意识，不要把每个元素独立起来，而是当成一个整体，比如上图第一次跳跃可以跳跃到`3`和`1`下标分别是`1`和`2`，要想跳得最远就要在`3`和`1`中挑选出能跳最远的元素我们记为`cur_max`。`cur_max`是从每次跳跃跨度和上一次跳跃的起点之间所有元素加上自身能跳跃最远的距离.

```C++
class Solution {
public:
    int jump(vector<int>& nums) {
        int cnt = 0;
        int pos_max = 0;      // 当前能跳最远的距离 nums[i] + i
        int cur_max = 0;      // 在一定的范围内能跳最远的距离
        for (int i = 0; i < nums.size() - 1; ++i){
            pos_max = max(nums[i] + i, pos_max);
            if (i == cur_max){		
                cur_max = pos_max;
                cnt++;
                if (cur_max >= nums.size() - 1) break;
            }
        }
        return cnt;
    }
};
```

