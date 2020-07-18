---
title: LeetCode 113.路径总和II
date: 2020-07-19 00:06:04
tags: ['LeetCode', '二叉树']
categories: 题解
---

#### [113. 路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)

<!--more-->

> 给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于给定目标和的路径。
>
> 说明: 叶子节点是指没有子节点的节点。
>
> 示例:
> 给定如下二叉树，以及目标和 sum = 22，
>
> ```
>               5
>              / \
>             4   8
>            /   / \
>           11  13  4
>          /  \    / \
>         7    2  5   1
> 
> ```
>
> 返回:
>
> ```
> [
>    [5,4,11,2],
>    [5,8,4,5]
> ]
> 
> ```

思路和112一样.
```C++
class Solution {
public:
    vector<vector<int>> pathSum(TreeNode* root, int sum) {
        if (!root)      return {};
        vector<int> cur;
        vector<vector<int>> ans;
        dfs(root, sum, cur, ans);
        return ans;
    }
    void dfs(TreeNode* root, int sum, vector<int>& cur, vector<vector<int>>& ans){
        if (!root)  return ;
        cur.push_back(root->val);
        if (sum == root->val && !root->left && !root->right)
            ans.push_back(cur);
        else{
            dfs(root->left, sum - root->val, cur, ans);
            dfs(root->right, sum - root->val, cur, ans);
        }
        cur.pop_back();             //恢复现场
    }
};
```

