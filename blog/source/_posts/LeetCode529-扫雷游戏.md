---
title: LeetCode529.扫雷游戏
date: 2020-08-20 01:03:05
tags: ['LeetCode', 'DFS']
categories: 题解
---

小时候一直不会玩扫雷，直到遇到这一道题才终于懂了扫雷的规则。

<!--more-->

#### [529. 扫雷游戏](https://leetcode-cn.com/problems/minesweeper/)

> 让我们一起来玩扫雷游戏！
>
> 给定一个代表游戏板的二维字符矩阵。 **'M'** 代表一个**未挖出**的地雷，'E' 代表一个未挖出的空方块，'B' 代表没有相邻（上，下，左，右，和所有4个对角线）地雷的**已挖出的空白方块**，**数字**（'1' 到 '8'）表示有多少地雷与这块**已挖出**的方块相邻，'X' 则表示一个已挖出的地雷。
>
> 现在给出在所有**未挖出的**方块中（'M'或者'E'）的下一个点击位置（行和列索引），根据以下规则，返回相应位置被点击后对应的面板：
>
> 1. 如果一个地雷（'M'）被挖出，游戏就结束了- 把它改为 **'X'**。
> 2. 如果一个没有**相邻地雷**的空方块（'E'）被挖出，修改它为（'B'），并且所有和其相邻的**未挖出**方块都应该被递归地揭露。
> 3. 如果一个**至少与一个地雷相邻**的空方块（'E'）被挖出，修改它为数字（'1'到'8'），表示相邻地雷的数量。
> 4. 如果在此次点击中，若无更多方块可被揭露，则返回面板。
> 示例 1：
> ```
> 输入: 
> [['E', 'E', 'E', 'E', 'E'],
>  ['E', 'E', 'M', 'E', 'E'],
>  ['E', 'E', 'E', 'E', 'E'],
>  ['E', 'E', 'E', 'E', 'E']]
> 
> Click : [3,0]
> 
> 输出: 
> [['B', '1', 'E', '1', 'B'],
>  ['B', '1', 'M', '1', 'B'],
>  ['B', '1', '1', '1', 'B'],
>  ['B', 'B', 'B', 'B', 'B']]
> ```
>
>  解释: ![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/08/Snipaste_2020-08-20_01-07-32.png)
>
>  **示例 2：** 
>
> ```
> 输入: 
> 
> [['B', '1', 'E', '1', 'B'],
>  ['B', '1', 'M', '1', 'B'],
>  ['B', '1', '1', '1', 'B'],
>  ['B', 'B', 'B', 'B', 'B']]
> 
> Click : [1,2]
> 
> 输出: 
> 
> [['B', '1', 'E', '1', 'B'],
>  ['B', '1', 'X', '1', 'B'],
>  ['B', '1', '1', '1', 'B'],
>  ['B', 'B', 'B', 'B', 'B']]
> ```
>
> ![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/08/Snipaste_2020-08-20_01-10-06.png)

#### 思路

主要是要理解游戏规则:

1. 当点击的点下面是雷,则将它改为`X` 游戏结束
2. 当点击的点没有与雷相邻则递归的将周围所有的方块都揭开
3. **如果当点击的点周围八个方向有雷,该点不再继续递归揭露.并且将它的数字改为`1-8`**
4.  如果在此次点击中，若无更多方块可被揭露，则返回面板 

```C++
class Solution {
public:
    vector<int> dire_x{-1, -1, -1, 0, 0, 1, 1,1};
    vector<int> dire_y{1, 0, -1, 1, -1, 1, 0, -1};
    vector<vector<char>> updateBoard(vector<vector<char>>& board, vector<int>& click) {
        if (board.empty())  return {};
        int x = click[0], y = click[1];
        if (board[x][y] == 'M'){
            board[x][y] = 'X';
            return board;
        }
        dfs(board, x, y);
        return board;
    }
    void dfs(vector<vector<char>>& board, int x, int y){
        if (x < 0 || x >= board.size() || y < 0 || y >= board[0].size()||
            board[x][y] == 'B' || ( board[x][y] >= '1' && board[x][y] <= '8' ))
            return ;
        int cnt = cnt_boom(board, x, y);
            // 当八个方向没有雷的时候 翻转为B
        if (cnt == 0)   board[x][y] = 'B';
        else{
            // 当八个方向有雷的时候 翻转为雷的数目, 并且不再该点递归下去了
            board[x][y] = cnt + '0';
            return ;
        }
         int cur_x = 0, cur_y = 0;
        for (int i = 0; i < 8; ++i){
            cur_x = x + dire_x[i];
            cur_y = y + dire_y[i];
            dfs(board, cur_x, cur_y);
        }
        return ;
    }

    int cnt_boom(const vector<vector<char>>& board, int x, int y){
        int cnt = 0;
        int cur_x = 0, cur_y = 0;
        for(int i = 0; i < 8; ++i){
            cur_x = x + dire_x[i];
            cur_y = y + dire_y[i];
            if (cur_x < 0 || cur_x >= board.size() || 
                cur_y < 0 || cur_y >= board[0].size())
                continue;
            if (board[cur_x][cur_y] == 'M')
                cnt++;
        }
        return cnt;
    }
};
```

