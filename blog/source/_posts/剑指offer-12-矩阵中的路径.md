---
title: 剑指Offer-12.矩阵中的路径
date: 2020-03-03 10:53:19
tags: 剑指offer
categories: 题解
---

这是一道很经典的`DFS`题，题目做了不少，但总感觉自己`DFS` 、`DP`、`递归`没摸到门路。

以后可能会多记录些做完后有收获感的这三类题。

<!--more-->

#### [面试题12. 矩阵中的路径](https://leetcode-cn.com/problems/ju-zhen-zhong-de-lu-jing-lcof/)

> 请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一格开始，每一步可以在矩阵中向左、右、上、下移动一格。如果一条路径经过了矩阵的某一格，那么该路径不能再次进入该格子。例如，在下面的3×4的矩阵中包含一条字符串“bfce”的路径（路径中的字母用加粗标出）。
>
> [["a","**b**","c","e"],
> ["s","**f**","**c**","s"],
> ["a","d","**e**","e"]]
>
> 但矩阵中不包含字符串“abfb”的路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入这个格子。
>
> 示例 1:
>
> ```
> 输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
> 输出：true
> ```
>
> 示例 2：
>
> ```
> 输入：board = [["a","b"],["c","d"]], word = "abcd"
> 输出：false
> ```
>
>
> **提示：**
>
> 1 <= board.length <= 200
> 1 <= board[i].length <= 200

#### 思路:DFS

对于一个矩阵，我们可以将它看做成一个无向图。为了在矩阵中搜索到相应的字符串，我们首先要用遍历的方法找到字符串中第一个字符在矩阵中的位置，然后再根据第一个字符串向四个方向深度遍历，如果存在着对应的字符串则返回`true`

```C++
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        int m = board.size(), n = board[0].size();
        vector<vector<bool>> visited(m,vector<bool>(n, false));
        for(int i = 0; i < m; ++i){
            for(int j = 0; j < n; ++j){
                if(dfs(board, word, i, j, 0, visited))
                    return true;
            }
        }
        return false;
    }
    bool dfs(vector<vector<char>>& board, string& word, int row, int col, int index, vector<vector<bool>>& visited){
        if(index == word.length())
            return true;
        if(row < 0 || col < 0 || row >= board.size() || col >= board[0].size())
            return false;
        if(visited[row][col] == true)      //已被访问过,则return false
            return false;
        if(board[row][col] != word[index]) //访问的元素与word对应的元素不符
            return false;
        //先将需要访问元素的位置置为true
        visited[row][col] = true;
        // 对访问的某个位置,向它的四周继续深度遍历
        if(dfs(board, word, row + 1, col, index + 1, visited)||
            dfs(board, word, row - 1, col, index + 1, visited)||
            dfs(board, word, row, col + 1, index + 1, visited)||
            dfs(board, word, row, col - 1, index + 1, visited))
            return true;
        // 不符合的位置,要还原状态
        visited[row][col] = false;
        return false;
    }
};
```



