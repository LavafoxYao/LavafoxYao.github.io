---
title: LeetCode 105.从前序与中序遍历序列构造二叉树
date: 2020-07-18 01:35:35
tags: ['LeetCode', '二叉树']
categories: 题解
---

#### [105. 从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

<!--more-->

> 根据一棵树的前序遍历与中序遍历构造二叉树。
>
> **注意:**
> 你可以假设树中没有重复的元素。
>
> 例如，给出
>
> ```
> 前序遍历 preorder = [3,9,20,15,7]
> 中序遍历 inorder = [9,3,15,20,7]
> ```
>
>  返回如下的二叉树： 
>
> ```
>     3
>    / \
>   9  20
>     /  \
>    15   7
> ```

**思路:** 重建二叉树的基本思路就是先构造根节点，再构造**左子树**，接下来构造**右子树**，其中，构造**左子树**和**右子树**是一个子问题，递归处理即可。

因此我们只关心如何构造根节点，以及如何递归构造左子树和右子树。 

我们可以利用前序遍历和中序遍历的特性来区分出左右子树的元素,然后递归构造.

PS: 对于区间分治的问题,最好是使用   **左闭右闭区间更加方便** .

```C++
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        if (preorder.empty() || inorder.empty())    return nullptr;
        return builder(preorder, 0, preorder.size() - 1, inorder, 0, inorder.size() -1);
    }
    TreeNode* builder(vector<int>& preorder, int pl, int pr, vector<int>& inorder, int il, int ir){
        if (pl > pr || il > ir) return nullptr;
        TreeNode* root = new TreeNode(preorder[pl]);
        int pos = 0;
        for (int i = il; i <= ir; ++i){
            if (inorder[i] == root->val){
                pos = i;
                break;
            }
        }
         // pos 前面是左子树, 后面是右子树
        int lt = pos - il;          // 左子树的元素个数
        int rt = ir - pos;          // 右子树的元素个数
        root->left = builder(preorder, pl + 1, pl + lt, inorder, il, il + lt - 1);
        root->right = builder(preorder, pl + lt + 1, pr, inorder, il + lt + 1, ir);
        return root;

    }
};
```

