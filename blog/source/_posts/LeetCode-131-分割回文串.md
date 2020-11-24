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

**上图蓝色方块里的字符串为当前"剩余的"，而如箭头上“截取a”是当前可选的答案.**

有了上图所示的递归树，代码的实现就容易很多了。

```C++
class Solution {
public:
    vector<vector<string>> partition(const string& s) {
        if (s.size() == 0)  return {};
        vector<vector<string>> ans;
        vector<string> cur;
        dfs(s, 0, cur, ans);
        return ans;
    }
    
    void dfs(const string& s, int idx, vector<string>& cur, vector<vector<string>>& ans){
        if (idx >= s.size()){
            ans.push_back(cur);
            return ;
        }
        
        for (int i = idx + 1; i <= s.size(); ++i){
            string str = s.substr(idx, i - idx);
            if (check(str)){
                cur.push_back(str);
                dfs(s, i, cur, ans);
                cur.pop_back();
            }
        }
        return ;
    }
    
	// 检查是否为回文串
    bool check(const string& str){
        int l = 0, r = str.size() - 1;
        while (l < r){
            if (str[l] != str[r])   return false;
            l++;
            r--;
        }
        return true;
    }
};
```

