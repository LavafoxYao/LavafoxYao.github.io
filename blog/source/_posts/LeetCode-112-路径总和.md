---
title: LeetCode 112.路径总和
date: 2020-07-18 23:40:52
tags: ['LeetCode', '二叉树']
categories: 题解
---

#### [112. 路径总和](https://leetcode-cn.com/problems/path-sum/)

<!--more-->

> 给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。
>
> 说明: 叶子节点是指没有子节点的节点。
>
> 示例: 
> 给定如下二叉树，以及目标和 sum = 22，
>
> ```
>         	  5
>              / \
>             4   8
>            /   / \
>           11  13  4
>          /  \      \
>         7    2      1
> ```
>
>  返回 `true`, 因为存在目标和为 22 的根节点到叶子节点的路径 `5->4->11->2`。 

思路: 二叉树的题目一定要懂递归的出口怎么定义,以及如何在左右子树上进行的操作.

```C++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int sum) {
        if (!root)      return false;
        return dfs(root, sum);
    }
    bool dfs(TreeNode* root, int sum){
        if (!root)  return false;
        // 当 sum - root->val == 0时 说明已经到达sum的值了
        // 再判断该节点是否是叶子结点
        else if (sum - root->val == 0 && !root->left && !root->right)   return true;
        // 由于带有返回值 要分别在 左右子树做递归操作 所以用 ||
        return dfs(root->left, sum - root->val) || dfs(root->right, sum - root->val);  
    }
};
```

