---
title: LeetCode22.括号生成
date: 2020-07-12 23:42:58
tags: LeetCode
categories:	题解
---

[22. 括号生成](https://leetcode-cn.com/problems/generate-parentheses/)

<!--more-->

> 数字 n 代表生成括号的对数，请你设计一个函数，用于能够生成所有可能的并且 有效的 括号组合。
>
>  
>
> 示例：
>
> ```
> 输入：n = 3
> 输出：[
>        "((()))",
>        "(()())",
>        "(())()",
>        "()(())",
>        "()()()"
>      ]
> ```

生成括号的过程可以用一颗二叉树来表示, 下图为 n = 1 时.

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/07/Snipaste_2020-07-12_23-34-47.png)

图2把不符合有效括号的节点进行了剪枝,生成一颗二叉树的过程也就是生成括号的过程.

```C++
class Solution {
public:
    vector<string> generateParenthesis(int n) {
        vector<string> ans;
        if (n == 0) return ans;
        dfs(n, n, "", ans);			// 以 "" 作为根节点
        return ans;
    }
    void dfs(int l, int r, string cur, vector<string>& ans){
        if (l == 0 && r == 0){			//当左右剩余的括号数为0 才完成了生成的过程
            ans.push_back(cur);
            return ;
        }
        //若左括号剩余大于右括号,则说明条件不成立,不能生成有效的括号
        if (l > r)  return ;
        if (l > 0)  
            dfs(l - 1, r, cur+ '(', ans);
        if (r > 0 ) 
            dfs(l, r - 1, cur + ')', ans);
        return ;
    }
};
```

