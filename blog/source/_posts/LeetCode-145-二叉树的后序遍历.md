---
title: LeetCode 145.二叉树的后序遍历
date: 2020-07-21 15:53:44
tags: ['LeetCode', '二叉树']
categories: 题解
---

[145. 二叉树的后序遍历](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

<!--more-->

> 给定一个二叉树，返回它的 *后序* 遍历。
>
>  **示例** 
>
> ```
> 输入: [1,null,2,3]  
>    1
>     \
>      2
>     /
>    3 
> 
> 输出: [3,2,1]
> ```

思路: 

1. 前序遍历的方式是 `root->left->right`的顺序.

2. 后序遍历的方式是`left->right->root`, 

3. 我们可以把前序改为后序先`root->right->left`然后`reverse`下就可以得到后序遍历了.

```C++
class Solution {
public:
    vector<int> postorderTraversal(TreeNode* root) {
        if (!root)  return {};
        vector<int> ans;
        stack<TreeNode*> st;
        TreeNode* cur = root;
        while (cur || !st.empty()){
            while (cur){
                ans.push_back(cur->val);       // 先root
                st.push(cur);
                cur = cur->right; 			   // 再 right
            }
            cur = st.top();
            st.pop();
            cur = cur->left;					//最后 left
        }
        // reverse 之后 left->right->root刚好为后序遍历的方式
        reverse(ans.begin(), ans.end());     
        return ans;
    }
};
```

