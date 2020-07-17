---
title: LeetCode 74.搜索二维矩阵
date: 2020-07-16 23:07:48
tags: ['LeetCode', '数组', '二分法']
categories: 题解
---

#### [74. 搜索二维矩阵](https://leetcode-cn.com/problems/search-a-2d-matrix/)

<!--more-->

> 编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：
>
> - 每行中的整数从左到右按升序排列。
> - 每行的第一个整数大于前一行的最后一个整数。
>
> **示例 1:**
>
> ```
> 输入:
> matrix = [
>   [1,   3,  5,  7],
>   [10, 11, 16, 20],
>   [23, 30, 34, 50]
> ]
> target = 3
> 输出: true
> ```
>
> **示例 2:**
>
> ```
> 输入:
> matrix = [
>   [1,   3,  5,  7],
>   [10, 11, 16, 20],
>   [23, 30, 34, 50]
> ]
> target = 13
> 输出: false
> ```

**思路:** 我这次做这一题的思路一开始没想到用二分法,就是使用的观察法....(可能之前做过类似的题叭)

**具体实现:**发现从左下角(或右上角)开始遍历, 如果当前指向的值大于目标值，则可以 “向上” 移动一行。 否则，如果当前指向的值小于目标值，则可以移动一列。 

```C++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        // 从左下角开始遍历
        if (matrix.empty()) return false;
        int row = matrix.size(), col = matrix[0].size();
        int cur_row = row - 1, cur_col = 0;                 // 左下角坐标
        while (cur_row >= 0 && cur_row < row && cur_col >= 0 && cur_col < col){
            if (target == matrix[cur_row][cur_col])     return true;
            else if(target > matrix[cur_row][cur_col])  cur_col++;
            else                                        cur_row--;
        }
        return false;
    }
};
```

**思路:**二分法

将二维数组转换成一维数组再使用二分法进行搜索.

关于转换的方法大致有个印象就行了,遇到具体的示例套一套就可以推出规则.

```C++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix.empty())     return false;
        int row = matrix.size(), col = matrix[0].size();
        int lo = 0, hi = row * col - 1;
        while (lo <= hi){
            int mid = lo + (hi - lo) / 2;
            if (matrix[mid / col][mid % col] == target) 	return true;
            else if(matrix[mid / col][mid % col] > target)  hi = mid - 1;
            else                                            lo = mid + 1;
        }
        return false;
    }
};
```

