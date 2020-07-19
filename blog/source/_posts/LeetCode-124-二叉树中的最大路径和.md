---
title: LeetCode 124.二叉树中的最大路径和
date: 2020-07-19 23:34:45
tags: ['LeetCode', '二叉树']
categories: 题解
---

#### [124. 二叉树中的最大路径和](https://leetcode-cn.com/problems/binary-tree-maximum-path-sum/)

<!--more-->

> 给定一个**非空**二叉树，返回其最大路径和。
>
> 本题中，路径被定义为一条从树中任意节点出发，达到任意节点的序列。该路径**至少包含一个**节点，且不一定经过根节点。
>
>  **示例 1:** 
>
> ```
> 输入: [1,2,3]
> 
>        1
>       / \
>      2   3
> 
> 输出: 6
> ```
>
>  **示例 2:** 
>
> ```
> 输入: [-10,9,20,null,null,15,7]
> 
>    -10
>    / \
>   9  20
>     /  \
>    15   7
> 
> 输出: 42
> ```
思路： 首先要理解`path`的意思，本题`path`以我的理解是一条不分叉的线段。
不分叉可以出现以下情况
```
 第一种：
    a
   / \			
  b   c
 
 第二种：
    a
   /
(左子树)

第三种：
    a
     \
     (右子树)
```
我所说的分叉的情况 如下图，d就是个分叉点：
```
         a
        /
       b
      /\
     d  f
```
所以出现最大路径的值有三种情况：
1. 当前节点加其左右子树（左右子树是单边分支）
2. 当前节点加左子树加其祖父节点（左右子树是单边分支）
3. 当前节点加右子树加其祖父节点（左右子树是单边分支）

```C++
class Solution {
public:
    int maxPathSum(TreeNode* root) {
        if (!root)  return 0;
        int ans = INT_MIN;
        helper(root, ans);
        return ans;
    }
    int helper(TreeNode* root, int& sum){
        if (!root)  return 0;
        int left = helper(root->left, sum);		// 左
        int right = helper(root->right, sum);	// 右
        int lmr = root->val + max(0, left) + max(0, right);// l m r 情景1
        int ret = root->val +max(0, max(left, right));	//单分支
        sum = max(sum, max(ret, lmr));			// 更新最大值
        return ret;								// 返回最大的单边
    }
};
```

