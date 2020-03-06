---
title: LeetCode-59.螺旋矩阵II
date: 2020-03-06 17:32:58
tags: LeetCode
categories: 题解
---

题目不难，面试应该考察的是`Bugfree`的能力了。

<!--more-->

#### [59. 螺旋矩阵 II](https://leetcode-cn.com/problems/spiral-matrix-ii/)

> 给定一个正整数 *n*，生成一个包含 1 到 *n*<sup>2</sup> 所有元素，且元素按顺时针顺序螺旋排列的正方形矩阵。
>
> **示例:**
>
> ```
> 输入: 3
> 输出:
> [
>  [ 1, 2, 3 ],
>  [ 8, 9, 4 ],
>  [ 7, 6, 5 ]
> ]
> ```

实质上就是通过控制遍历的方向来实现螺旋， 先向右遍历，到右端向下遍历到底部，向左遍历，再向上遍历，如此反复，直到右边界缩小小于左边界，或则上边界缩小小于下边界，至此遍历完成。具体看下图（图来自于LeetCode: Krahets）

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/03/Snipaste_2020-03-06_17-37-11.png)

```C++
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        //向右==>向下==>向左==>向上
        vector<vector<int>> ans(n, vector<int>(n));;
        int top = 0, bottom = n - 1, left = 0, right = n - 1;
        int index = 1;
        while(1){
            // 向右
            for(int i = left; i <= right; ++i)
                ans[top][i] = index++;
            top++;
            if(top > bottom || left > right)
                break;

            // 向下
            for(int i = top; i <= bottom; ++i)
                ans[i][right] = index++;
            right--;
            if(top > bottom || left > right)
                break;

            // 向左
            for(int i = right; i >= left; --i)
                ans[bottom][i] = index++;
            bottom--;
            if(top > bottom || left > right)
                break;
            
            // 向上
            for(int i = bottom; i >= top; --i)
                ans[i][left] = index++;
            left++;
            if(top > bottom || left > right)
                break;
        }
        return ans;
    }
};
```

