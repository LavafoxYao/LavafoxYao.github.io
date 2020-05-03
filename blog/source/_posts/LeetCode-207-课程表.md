---
title: LeetCode-207.课程表
date: 2020-05-03 20:56:52
tags:  ['LeetCode', 'DFS', 'BFS']
categories:  题解
---

拓扑排序是图中常见的一种用于检测是否存在环的算法。

当且仅当图（有向无环图）没有环时，才可能进行拓扑排序。

[拓扑排序](<https://zh.wikipedia.org/wiki/%E6%8B%93%E6%92%B2%E6%8E%92%E5%BA%8F>)

<!--more-->

#### [207. 课程表](https://leetcode-cn.com/problems/course-schedule/)

> 你这个学期必须选修 numCourse 门课程，记为 0 到 numCourse-1 。
>
> 在选修某些课程之前需要一些先修课程。 例如，想要学习课程 0 ，你需要先完成课程 1 ，我们用一个匹配来表示他们：[0,1]
>
> 给定课程总量以及它们的先决条件，请你判断是否可能完成所有课程的学习？
>
> **示例 1:**
>
> ```
> 输入: 2, [[1,0]] 
> 输出: true
> 解释: 总共有 2 门课程。学习课程 1 之前，你需要完成课程 0。所以这是可能的。
> ```
>
> **示例 2:**
>
> ```
> 输入: 2, [[1,0],[0,1]]
> 输出: false
> 解释: 总共有 2 门课程。学习课程 1 之前，你需要先完成课程 0；并且学习课程 0 之前，你还应先完成课程 1。这是不可能的。
> ```

### DFS

题目本质就是判断给出的课程是否存在环

```C++
class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> graph (numCourses); //构建邻接表
        vector<int> visit(numCourses, 0);       //三种状态 0 未访问 1正在访问 2 已经访问
        for (auto iter : prerequisites)
            graph[iter[1]].push_back(iter[0]);
        for (int i = 0; i < numCourses; ++i)
            if (dfs(graph, visit,i )) return false;
     
        return true;
    }
    bool dfs(vector<vector<int>>& graph, vector<int>& visit, int cur){
        if (visit[cur] == 1)    return true;  // 表示存在环
        if (visit[cur] == 2)    return false; // 表示不存在环
        visit[cur] = 1;
        for (auto iter : graph[cur]) //遍历 cur 的所有邻居(有出度的)
            if (dfs(graph, visit, iter))    return true;
        visit[cur] = 2;
        return false;
    }
};
```

