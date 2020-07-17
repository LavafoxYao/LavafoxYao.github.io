---
title: LeetCode 72.编辑距离
date: 2020-07-17 00:01:29
tags: ['LeetCode', 'DP', '贪心']
categories: 题解
---

#### [72. 编辑距离](https://leetcode-cn.com/problems/edit-distance/)

<!--more-->

> 给你两个单词 word1 和 word2，请你计算出将 word1 转换成 word2 所使用的最少操作数 。
>
> 你可以对一个单词进行如下三种操作：
>
> 1. 插入一个字符
> 2. 删除一个字符
> 3. 替换一个字符
>
>  **示例 1：** 
>
> ```
> 输入：word1 = "horse", word2 = "ros"
> 输出：3
> 解释：
> horse -> rorse (将 'h' 替换为 'r')
> rorse -> rose (删除 'r')
> rose -> ros (删除 'e')
> ```
>
>  **示例 2：** 
>
> ```
> 输入：word1 = "intention", word2 = "execution"
> 输出：5
> 解释：
> intention -> inention (删除 't')
> inention -> enention (将 'i' 替换为 'e')
> enention -> exention (将 'n' 替换为 'x')
> exention -> exection (将 'n' 替换为 'c')
> exection -> execution (插入 'u')
> ```
>
> 

思路:这题拿到手的时候我是一脸懵逼的，找了很多题解看，开始不是很懂很多人直接写的DP

这里推荐[大红豆小薏米]( https://www.bilibili.com/video/BV17t411g76R?from=search&seid=7591719058782487803 )

妹子的讲解思路很清晰，我也看了很多图文的讲解，只不过大部分时候我是被讲的云里雾里，既然小姐姐讲的这么清晰，我就不再赘述思路了。

```C++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int wd1_len = word1.size(), wd2_len = word2.size();
        vector<vector<int>> dp(wd1_len + 1, vector<int>(wd2_len + 1, 0));
        for (int i = 0; i <= wd1_len; ++i){
            for (int j = 0; j <= wd2_len; ++j){
                if (i == 0){
                    dp[0][j] = j;
                    continue;
                }
                if (j == 0){
                    dp[i][0] = i;
                    continue;
                }
                if (word1[i - 1] == word2[j - 1])   dp[i][j] = dp[i - 1][j - 1];
                else 
                    dp[i][j] = min(dp[i - 1][j], min(dp[i][j - 1], dp[i - 1][j - 1]))+1;
            }
        }
        return dp[wd1_len][wd2_len];
    }
};
```





