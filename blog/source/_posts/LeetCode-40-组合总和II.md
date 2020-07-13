---
title: LeetCode 40.组合总和II
date: 2020-07-14 00:06:27
tags: ['LeetCode', 'DFS']
categories: 题解
---

#### [40. 组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/)

<!--more-->

> 给定一个数组 candidates 和一个目标数 target ，找出 candidates 中所有可以使数字和为 target 的组合。
>
> candidates 中的每个数字在每个组合中只能使用一次。
>
> 说明：
>
> - 所有数字（包括目标数）都是正整数。
>
> - 解集不能包含重复的组合。 
>
>   

> **示例 1:**
>
> ```
> 输入: candidates = [10,1,2,7,6,1,5], target = 8,
> 所求解集为:
> [
>   [1, 7],
>   [1, 2, 5],
>   [2, 6],
>   [1, 1, 6]
> ]
> ```
>
> **示例 2:**
>
> ```
> 输入: candidates = [2,5,2,1,2], target = 5,
> 所求解集为:
> [
>   [1,2,2],
>   [5]
> ]
> ```

`40`题和`39`题是同源题,较之`39`题需要去重.

去重最容易想到的就是使用`STL`中的`SET`

```C++
class Solution {
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        set<vector<int>> ans;
        vector<int> cur;
        sort(candidates.begin(), candidates.end());
        dfs(candidates, target, 0, cur, ans);
        return vector<vector<int>> (ans.begin(), ans.end());  // 利用set中的元素构造vector
    }
    void dfs(const vector<int>& candidates, int target, int s, vector<int>& cur, set<vector<int>>& ans){
        if (target == 0){
            ans.insert(cur);
            return ;
        }
        for (int i = s;i < candidates.size(); ++i){
            if (candidates[i] > target) return;
            cur.push_back(candidates[i]);
            dfs(candidates, target - candidates[i], i + 1, cur, ans);
            cur.pop_back();
        }

    }
};
```

上面用`set`是利用`set`元素唯一性的特性,也属于是偷懒的方法.

常规来说是利用合理的剪枝更合适.

```C++
class Solution {
public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<int> cur;
        vector<vector<int>> ans;
        sort(candidates.begin(), candidates.end());
        dfs(candidates, target, 0, cur, ans);
        return ans;
    }
    void dfs(const vector<int>& candidates, int target, int s, vector<int>& cur, vector<vector<int>>& ans){
        if (target == 0){
            ans.push_back(cur);
            return;
        }
        for (int i = s; i < candidates.size(); ++i){
            if (candidates[i] > target)     return;
            // 如果 i不是被选中的第一个元素, 就可以和被选中的上一个元素比较是否相同
            // 若相同则结束此次循环
            if (i > s && candidates[i - 1] == candidates[i])    continue;// 注意这种剪枝方法
            cur.push_back(candidates[i]);
            dfs(candidates, target - candidates[i], i + 1, cur, ans);
            cur.pop_back();
        }
    }
};
```

