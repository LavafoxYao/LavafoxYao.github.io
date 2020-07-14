---
title: LeetCode 47.全排列II
date: 2020-07-14 23:01:28
tags: ['LeetCode', 'DFS']
categories: 题解
---

#### [47. 全排列 II](https://leetcode-cn.com/problems/permutations-ii/)

<!--more-->

> 给定一个可包含重复数字的序列，返回所有不重复的全排列。
>
> 示例:
>
> 输入: [1,1,2]
> 输出:
> [
>   [1,1,2],
>   [1,2,1],
>   [2,1,1]
> ]

`47题`与`46题`最大的不同就是在`47题`给的原始数组中包含了重复的数字，所以也衍生出去重的问题，和组合数的思路一样。想到去重第一反应就是可以使用`set`.

```C++
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<int> cur;
        vector<bool> visited(nums.size(), false);
        set<vector<int>> ans;
        dfs(nums, visited, cur, ans);
        return vector<vector<int>>(ans.begin(), ans.end());
    }
    void dfs(const vector<int>& nums, vector<bool>& visited, vector<int>& cur, set<vector<int>>& ans){
        if (cur.size() == nums.size()){
            ans.insert(cur);
            return;
        }
        for (int i = 0; i < nums.size(); ++i){
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

上面使用`set`可以在实在想不出怎么剪枝的前提下使用，在练习的过程中还是要学会怎么去使用剪枝来获得高效的代码。

```C++
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> cur;
        vector<bool> visited(nums.size(), false);
        sort(nums.begin(), nums.end());         // 需要排序, 是的相同数值的数在一起
        dfs(nums, visited, cur, ans);
        return ans;
    }
    void dfs(const vector<int>& nums, vector<bool>& visited, vector<int>& cur, vector<vector<int>>& ans){
        if (nums.size() == cur.size()){
            ans.push_back(cur);
            return ;
        }
        for (int i = 0; i < nums.size(); ++i){
            if (!visited[i]){
                // 当生成树的同一层有使用相同数值的节点 就将其剪枝
                // 同一层被使用过的数字 在visited中标记为true
                if(i > 0 && visited[i - 1] && nums[i - 1] == nums[i]) continue;
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

