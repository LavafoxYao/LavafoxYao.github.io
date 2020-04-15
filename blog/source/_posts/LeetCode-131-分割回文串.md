---
title: LeetCode-131.分割回文串
date: 2020-04-11 22:15:35
tags: ['LeetCode', '回溯']
categories: 题解
---

初试想到是用`DFS`，但是没想明白是如何进行回溯的，直到看到[liweiwei1319题解](<https://leetcode-cn.com/problems/palindrome-partitioning/solution/hui-su-you-hua-jia-liao-dong-tai-gui-hua-by-liweiw/>)

<!--more-->

看了大佬的题解，我发现每次考虑`DFS`问题的时候，要做到心中有棵递归树。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/04/Snipaste_2020-04-11_22-20-15.png)

有了上图所示的递归树，代码的实现就容易很多了。

```C++
class Solution {
public:
    vector<vector<string>> partition(string s) {
        vector<vector<string>> ans;
        if (s.size() == 0)  return ans;
        vector<string> cur;
        dfs(s, ans, cur);
        return ans;
    }
    bool check(string str){
        if (str.size() == 0) return true;
        int left = 0, right = str.size() - 1;
        while (left < right){
            if (str[left++] != str[right--]) return false;
        }
        return true;
    }
    void dfs(string s, vector<vector<string>>& ans, vector<string>& cur){
        if (s.empty()){
            // 当s被截取成空串说明,截取的每个部分都是回文串
            ans.push_back(cur);
            return;
        }
        for (int i = 0; i < s.size(); ++i){
            string str = s.substr(0, i + 1);
            if (check(str)){
                cur.push_back(str);
                dfs(s.substr(i+1), ans, cur);
                cur.pop_back();         //  恢复现场
            }
        }
    }
};
```

