---
title: LeetCode 94.二叉树的中序遍历
date: 2020-07-17 23:54:47
tags: ['LeetCode', '递归', '二叉树']
categories: 题解
---

#### [94. 二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

<!--more-->

> 给定一个二叉树，返回它的中序 遍历。
>
> 示例:
>
> ```
> 输入: [1,null,2,3]
>    1
>     \
>      2
>     /
>    3
> 
> 输出: [1,3,2]
> ```

### 递归

```C++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        if (!root)  return {};
        vector<int> ans;
        inorder(root, ans);
        return ans;
    }
    void inorder(TreeNode* root, vector<int>& ans){
        if (!root)  return;
        if (root->left) inorder(root->left, ans);
        ans.push_back(root->val);
        if (root->right) inorder(root->right, ans);
    }
};
```

### 迭代

记住要用`stack`作为容器, 层次遍历用`deque`

```C++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        if (!root)  return {};
        stack<TreeNode*> st;
        vector<int> ans;
        while (root || !st.empty()){
            while (root){
                st.push(root);
                root = root->left;
            }
            root = st.top();
            ans.push_back(root->val);
            st.pop();
            root = root->right;
        }
        return ans;
    }
};
```

