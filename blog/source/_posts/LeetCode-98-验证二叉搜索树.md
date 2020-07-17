---
title: LeetCode 98.验证二叉搜索树
date: 2020-07-18 01:29:00
tags: ['LeetCode', '二叉树']
categories: 题解
---

#### [98. 验证二叉搜索树](https://leetcode-cn.com/problems/validate-binary-search-tree/)

<!--more-->

> 给定一个二叉树，判断其是否是一个有效的二叉搜索树。
>
> 假设一个二叉搜索树具有如下特征：
>
> - 节点的左子树只包含小于当前节点的数。
> - 节点的右子树只包含大于当前节点的数。
> - 所有左子树和右子树自身必须也是二叉搜索树。
>
>  **示例 1:** 
>
> ```
> 输入:
>     2
>    / \
>   1   3
> 输出: true
> ```
>
>  **示例 2:** 
>
> ```
> 输入:
>     5
>    / \
>   1   4
>      / \
>     3   6
> 输出: false
> 解释: 输入为: [5,1,4,null,null,3,6]。
>      根节点的值为 5 ，但是其右子节点值为 4 。
> ```



**思路:根据搜索二叉树的特性,中序遍历后得到的序列为从小到大的有序序列这一特性,遍历二叉树.**

```C++
class Solution {
public:
    bool isValidBST(TreeNode* root) {
      if (!root)    return true;
      vector<int> buf;
      inorder(root, buf);
      for (int i = 1; i < buf.size(); ++i){
          if (buf[i - 1] >= buf[i])  return false;
      }
        return true;
    }
    void inorder(TreeNode* root, vector<int>& ans){
        if (!root)  return;
        stack<TreeNode*> st;
        while (root || !st.empty()){
            while (root){
                st.push(root);
                root = root->left;
            }
            root = st.top();
            st.pop();
            ans.push_back(root->val);
            root = root->right;
        }
    }
};
```



