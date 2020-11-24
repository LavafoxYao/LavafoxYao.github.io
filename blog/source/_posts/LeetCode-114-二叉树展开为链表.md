---
title: LeetCode 114.二叉树展开为链表
date: 2020-11-22 09:54:10
tags: ['LeetCode', '二叉树']
categories: 题解
---

#### [114. 二叉树展开为链表](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

<!--more-->

> 给定一个二叉树，原地将它展开为一个单链表。
>
>  
>
> 例如，给定二叉树
>
>        1
>       / \
>       2   5
>      / \   \
>     3   4   6
> 将其展开为：
>
> ```
> 1
>  \
>   2
>    \
>     3
>      \
>       4
>        \
>         5
>          \
>           6
> ```

思路: 先思考如果只有三个节点应该怎么去做(树最简单的切入方法就是, 考虑三个节点的时候应该怎么做)

1. 首先要把左孩子置为`nullptr`
2. 然后把左孩子挂到链表上
3. 右孩子重复上述过程

所以我们可以利用一个变量去记录当前链表的尾部，这样每次挂节点直接指下指针就可以了，具体的思路见代码。*（树这种递归结构的数据结构，再写代码之前一定要把处理的细节思考清楚。*
```C++
class Solution {
public:
    // 记录链表最后一个节点
    TreeNode* list_tail = nullptr;

    void flatten(TreeNode* root) {
        if (!root)  return ;
        TreeNode* l = root->left;
        TreeNode* r = root->right;
        // 将左孩子置空,因为我们再上一步已经记录左孩子了, 不怕找不到
        root->left = nullptr;      
        if (list_tail)
            list_tail->right = root;    
        list_tail = root;   // 将list_tail向后移动一位.
          
        flatten(l);         // 左子树如此去处理
        flatten(r);         // 右子树也如此去处理
    }
};
```

