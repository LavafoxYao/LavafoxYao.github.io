---
title: LeetCode-542.01矩阵
date: 2020-04-15 12:05:11
tags: ['LeetCode', 'BFS']
categories: 题解
---

每日一题已经出了很多关于多点`BFS`的题目，但是今天我在做这个题目的时候并没有做出来，看了官方的题解才多对多点的`BFS`有更深的感悟，这也是第一次感觉到官方题解写的很棒。

<!--more-->

#### [542. 01 矩阵](https://leetcode-cn.com/problems/01-matrix/)

> 给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。
>
> 两个相邻元素间的距离为 1 。
>
> **示例 1:**
> 输入:
>
> ```
> 0 0 0
> 0 1 0
> 0 0 0
> ```
>
> 输出:
>
> ```
> 0 0 0
> 0 1 0
> 0 0 0
> ```
>
> **示例 2:**
> 输入:
>
> ```
> 0 0 0
> 0 1 0
> 1 1 1
> ```
>
> 输出:
>
> ```
> 0 0 0
> 0 1 0
> 1 2 1
> ```
>
> **注意:**
>
> 1. 给定矩阵的元素个数不超过 10000。
> 2. 给定矩阵中至少有一个元素是 0。
> 3. 矩阵中的元素只在四个方向上相邻: 上、下、左、右。

官方的题解很不错特别是图[官方题解](<https://leetcode-cn.com/problems/01-matrix/solution/01ju-zhen-by-leetcode-solution/>)

**题目解释: 对于矩阵中的每一个元素，如果它的值为 0，那么离它最近的 0 就是它自己。如果它的值为 1，那么我们就需要找出离它最近的 0，并且返回这个距离值。**

为了方便解释我会使用题解中的图

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/04/Snipaste_2020-04-15_13-12-56.png)

**由于每个入队的0是同时向周围扩张的所以最先修改的距离就是最短的距离**

正如官方题解给出的`超级零`的概念，就是将首先入队的所有的`0`看做成一个0.

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/04/Snipaste_2020-04-15_13-21-46.png)

熟悉「最短路」的读者应该知道，我们所说的「超级零」实际上就是一个「超级源点」。在最短路问题中，如果我们要求多个源点出发的最短路时，一般我们都会建立一个「超级源点」连向所有的源点，用「超级源点」到终点的最短路等价多个源点到终点的最短路。

```C++
class Solution {
public:
    vector<vector<int>> updateMatrix(vector<vector<int>>& matrix) {
        int row = matrix.size(), col = matrix[0].size();
        if (row == 0 || col == 0)   return {{}};
        queue<pair<int, int>> q;
        // 标记遍历过的元素
        vector<vector<int>> visited(row, vector<int>(col, 0));
        // 将0入队列形成一个超级源点
        for (int i = 0; i < row; ++i){
            for (int j = 0; j < col; ++j){
                if (matrix[i][j] == 0){
                    q.push({i, j});
                    visited[i][j] = 1;
                }
            }
        }
        // BFS
        vector<int> dire_x {1, -1, 0, 0};
        vector<int> dire_y {0, 0, 1, -1};
        while (!q.empty()){
            int size = q.size();
            for (int i = 0; i < size; ++i){
                int tmp_x = q.front().first;
                int tmp_y = q.front().second;
                q.pop();
                for (int j = 0; j < 4; ++j){
                    int x = tmp_x + dire_x[j];
                    int y = tmp_y + dire_y[j];
                    if (x >= 0 && x < row && y >= 0 && y < col && !visited[x][y]){
                        matrix[x][y] = matrix[tmp_x][tmp_y] + 1;
                        visited[x][y] = 1;
                        q.push({x,y});
                    }
                }
            }
        }
        return matrix;
    }
};
```

