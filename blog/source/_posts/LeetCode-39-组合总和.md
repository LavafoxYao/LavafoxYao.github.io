---
title: LeetCode 39.组合总和
date: 2020-07-13 23:06:13
tags: ['LeetCode', 'DFS']
categories: 题解
---

#### [39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

<!--more-->

> 给定一个无重复元素的数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
>
> candidates 中的数字可以无限制重复被选取。
>
> **说明：**
>
> 所有数字（包括 target）都是正整数。
> 解集不能包含重复的组合。 
> **示例 1：**
>
> ```
> 输入：candidates = [2,3,6,7], target = 7,
> 所求解集为：
> [
>   [7],
>   [2,2,3]
> ]
> ```
>
> **示例 2：**
>
> ```
> 输入：candidates = [2,3,5], target = 8,
> 所求解集为：
> [
>   [2,2,2,2],
>   [2,3,3],
>   [3,5]
> ]
> ```

思路: 本题是一个很传统的利用`DFS`实现`组合`的题目.组合的过程也是一个生成多叉树的过程,在生成树的过程中需要进行剪枝.根据题目所知可以使用重复的元素,则多叉树的叉数等于`candidates的长度`. 

这里需要在`DFS`之前要排序下,使得剪枝的过程更有序.

下图 以示例2为例.

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/07/Snipaste_2020-07-13_23-56-07.png)



```C++
class Solution {
public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int> cur;
        sort(candidates.begin(), candidates.end());		// 排序 使之从小到大
        dfs(candidates, target, 0, cur, ans);
        return ans;
    }
    void dfs(const vector<int>& candidates, int target, int s,vector<int>& cur, vector<vector<int>>& ans){
        if (target == 0){
            ans.push_back(cur);
            return;
        }
        for (int i = s; i < candidates.size(); ++i){
            if (candidates[i] > target)   return;			//剪枝
            cur.push_back(candidates[i]);
            dfs(candidates, target - candidates[i], i, cur, ans);
            cur.pop_back();
        }

    }
};
```

