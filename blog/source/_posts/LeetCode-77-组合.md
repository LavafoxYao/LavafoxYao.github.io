---
title: LeetCode-77.组合
date: 2020-03-08 12:41:26
tags: ['LeetCode', '回溯']
categories: 题解
---

组合（combination）也是利用`dfs`求解的经典问题，今天就让我们一起来归纳一下。

<!--more-->

#### [77. 组合](https://leetcode-cn.com/problems/combinations/)

给定两个整数 *n* 和 *k*，返回 1 ... *n* 中所有可能的 *k* 个数的组合。

> **示例:**
>
> ```
> 输入: n = 4, k = 2
> 输出:
> [
>   [2,4],
>   [3,4],
>   [2,3],
>   [1,2],
>   [1,3],
>   [1,4],
> ]
> ```

分析回溯问题，最好是能结合图来帮助我们更好的理清思路以及快速的找到边界条件。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/03/Snipaste_2020-03-08_12-48-37.png)

我们设根节点是空集，每次挑选一个元素后，下一层的挑选就要前进一步，如果`cur`中的元素数目达到了所给的`k`则找到了符合条件的一个集合。

```C++
class Solution {
public:
    vector<vector<int>> combine(int n, int k) {
        vector<vector<int>> ans;
        vector<int> cur;
        dfs(n, k, 1, ans, cur);
        return ans;
    }
    void dfs(int n, int k, int index, vector<vector<int>>& ans, vector<int>& cur){
        if(cur.size() == k){                
            ans.emplace_back(cur);
            return;
        }
        for(int i = index; i <= n; ++i){
            cur.push_back(i); 
            dfs(n, k, i + 1, ans, cur);     // 进入下一层的时候不能选取已经选过的元素所以要i+1
            cur.pop_back();                 //  回溯,恢复现场
        }
        return ;
    }
};
```

