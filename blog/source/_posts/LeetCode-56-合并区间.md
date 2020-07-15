---
title: LeetCode 56.合并区间
date: 2020-07-15 21:48:30
tags: ['LeetCode', '数组']
categories:	题解
---

#### [56. 合并区间](https://leetcode-cn.com/problems/merge-intervals/)

<!--more-->

> 给出一个区间的集合，请合并所有重叠的区间。
>
> **示例 1:**
>
> ```
> 输入: [[1,3],[2,6],[8,10],[15,18]]
> 输出: [[1,6],[8,10],[15,18]]
> 解释: 区间 [1,3] 和 [2,6] 重叠, 将它们合并为 [1,6].
> ```
>
> **示例 2:**
>
> ```
> 输入: [[1,4],[4,5]]
> 输出: [[1,5]]
> 解释: 区间 [1,4] 和 [4,5] 可被视为重叠区间。
> ```

思路: 有点类似于[45.跳跃游戏]( https://leetcode-cn.com/problems/jump-game-ii/ )

先需要将区间首个元素值从小到大排列,再两两合并区间.

```C++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        if (intervals.size() == 0)  return {};
		// 区间首个元素值从小到大排列
        sort(intervals.begin(), intervals.end(), 
            [](const vector<int>& A, const vector<int>& B){
                return A[0] < B[0];});

        vector<vector<int>> ans;
        int cur_l = intervals[0][0], cur_r = intervals[0][1];
        int i = 1;
        for (; i < intervals.size(); ++i){
            // 重叠区间
            if (cur_r >= intervals[i][1] || 
                (cur_r >= intervals[i][0] && cur_r <= intervals[i][1])){
                if (cur_r < intervals[i][1])    cur_r = intervals[i][1];
                continue;
            }
            // 非重叠区间
            ans.push_back(vector<int>{cur_l, cur_r});
            cur_l = intervals[i][0];
            cur_r = intervals[i][1];
        }
        if (i == intervals.size())
            ans.push_back(vector<int>{cur_l, cur_r});
        return ans;
    }
};
```

