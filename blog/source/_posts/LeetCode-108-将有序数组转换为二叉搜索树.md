---
title: LeetCode 108.将有序数组转换为二叉搜索树
date: 2020-07-18 02:40:24
tags: ['LeetCode', '二叉树', '二分法']
categories: 题解
---

#### [108. 将有序数组转换为二叉搜索树](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

<!--more-->

> 将一个按照升序排列的有序数组，转换为一棵高度平衡二叉搜索树。
>
> 本题中，一个高度平衡二叉树是指一个二叉树*每个节点* 的左右两个子树的高度差的绝对值不超过 1。
>
> **示例:** 
>
> ```
> 给定有序数组: [-10,-3,0,5,9],
> 
> 一个可能的答案是：[0,-3,9,-10,null,5]，它可以表示下面这个高度平衡二叉搜索树：
> 
>    0
>   / \
> -3   9
> /   /
> -10  5
> ```

PS: 注意运用区间分治的时候最好使用左闭右闭.

```C++
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        if (nums.empty())   return nullptr;
        return builder(nums, 0, nums.size() - 1);
    }
    TreeNode* builder(vector<int>& nums, int lo, int hi){
        if (lo > hi)   return nullptr;
        int mid = lo + (hi - lo) / 2;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = builder(nums, lo, mid - 1);
        root->right = builder(nums, mid + 1, hi);
        return root;
    }
};
```

