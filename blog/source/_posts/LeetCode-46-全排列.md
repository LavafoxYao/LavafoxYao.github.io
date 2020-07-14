---
title: LeetCode 46.全排列
date: 2020-07-14 22:39:11
tags: ['LeetCode', 'DFS']
categories: 题解
---

 [46. 全排列](https://leetcode-cn.com/problems/permutations/)

全排列的问题我之前刚开始刷题的时候也写了一篇`blog`，但是当我时隔数月再做此题的时候又是不同的感受，特别是在昨天把组合数的题目也写了一遍的前提下，于是有了这篇`blog`。

<!--more-->

> 给定一个 没有重复 数字的序列，返回其所有可能的全排列。
>
> 示例:
>
> ```
> 输入: [1,2,3]
> 输出:
> [
>   [1,2,3],
>   [1,3,2],
>   [2,1,3],
>   [2,3,1],
>   [3,1,2],
>   [3,2,1]
> ]
> ```

思路: 组合数和排列数原理上是一样的，都是利用递归来形成生成树，并且通过合理的减枝获得符合条件的结果。

从代码的形式上来看，`permutation`比`combination`多了一个记录数组。

这个记录数组的作用是使得生成树的某一条路径上的元素不会出现同一个元素(不是指值相等,而是同一个).

```C++
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
       vector<int> cur;
       vector<bool> visited(nums.size(), false);    // 记录访问数据的数组
       vector<vector<int>> ans;
       dfs(nums, visited, cur, ans);
       return ans;
    }
    void dfs(const vector<int>& nums, vector<bool>& visited, vector<int>& cur, vector<vector<int>>& ans){
        if (cur.size() == nums.size()){
            ans.push_back(cur);
            return;
        }
        for(int i = 0; i < nums.size(); ++i){
            if (!visited[i]){
                visited[i] = true;
                cur.push_back(nums[i]);
                dfs(nums, visited, cur, ans);
                visited[i] = false;
                cur.pop_back();
            }
        }
    }
};
```

