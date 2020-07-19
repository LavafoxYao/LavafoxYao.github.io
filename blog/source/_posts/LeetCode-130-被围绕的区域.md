---
title: LeetCode 130.被围绕的区域
date: 2020-07-20 01:20:07
tags: ['LeetCode', 'DFS']
categories: 题解
---

#### [130. 被围绕的区域](https://leetcode-cn.com/problems/surrounded-regions/)

<!--more-->

> 给定一个二维的矩阵，包含 `'X'` 和 `'O'`（**字母 O**）。
>
> 找到所有被 `'X'` 围绕的区域，并将这些区域里所有的 `'O'` 用 `'X'` 填充。
>
> **示例:**
>
> ```
> X X X X
> X O O X
> X X O X
> X O X X
> ```
>
> 运行你的函数后，矩阵变为：
>
> ```
> X X X X
> X X X X
> X X X X
> X O X X
> ```
>
> **解释:**
>
> 被围绕的区间不会存在于边界上，换句话说，任何边界上的 'O' 都不会被填充为 'X'。 任何不在边界上，或不与边界上的 'O' 相连的 'O' 最终都会被填充为 'X'。如果两个元素在水平或垂直方向相邻，则称它们是“相连”的。

思路：题目需要将所有被`X`包围的`O`找出来，那剩下的`O`就是连接起来与边界连通的`O`。**直接找出来所有被`X`包围的`O`并不好找**(写了很多边界条件 ac不了.....)，但我们可以逆向思维：先找到所有连接起来与边界连通的`O`，将这些`O`标记一下，然后遍历数组，所有没有被标记的`O`就是我们要找的`O`。

```C++
class Solution {
public:
    void solve(vector<vector<char>>& board) {
        // leetcode 题目传参带有vector 都需要判断是否为空
        if (!board.size() || !board[0].size())  return;
        int row = board.size(), col = board[0].size();
        // 先把二维矩阵边界上‘O’以及与其连通的'O'标记为‘V’
        // 被标记为‘V’说明 不能被‘X’包围掉 ==》 转换为X
        // 第一行和最后一行
        for (int i = 0; i < col; ++i){
            dfs(board, 0, i);           //  第一行
            dfs(board, row - 1, i);     //  最后行
        }
        for (int i = 0; i < row; ++i){
            dfs(board, i, 0);           //  第一列
            dfs(board, i, col - 1);     //  最后列
        }
        for (int i = 0; i < row; ++i){
            for (int j = 0; j < col; ++j){
                if (board[i][j] == 'V')         board[i][j] = 'O';   // 与边界O连通的O
                else if (board[i][j] == 'O')    board[i][j] = 'X';   // 被X包围的O
            }
        }
        return;
    }
    
    void dfs(vector<vector<char>>& board, int i, int j){
        if (i < 0 || i >= board.size() || j < 0 || j >= board[0].size() || board[i][j] != 'O')      return;
        board[i][j] = 'V';
        // 四个方向dfs
        dfs(board, i - 1, j);
        dfs(board, i + 1, j);
        dfs(board, i, j - 1);
        dfs(board, i, j + 1);
    }
};
```

