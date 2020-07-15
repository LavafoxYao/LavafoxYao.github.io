---
title: LeetCode 49.字母异位词分组
date: 2020-07-15 21:10:48
tags: ['LeetCode', 'Hash']
categories:	题解
---

#### [49. 字母异位词分组](https://leetcode-cn.com/problems/group-anagrams/)

<!--more-->

> 给定一个字符串数组，将字母异位词组合在一起。字母异位词指字母相同，但排列不同的字符串。
>
> **示例:**
>
> ```
> 输入: ["eat", "tea", "tan", "ate", "nat", "bat"]
> 输出:
> [
>   ["ate","eat","tea"],
>   ["nat","tan"],
>   ["bat"]
> ]
> ```
>
> 说明：
>
> 所有输入均为小写字母。
> 不考虑答案输出的顺序。

**思路**：遍历字符串数组，并且每取一次字符串将其排序，然后存储到`map`中，这样就可以使得字母异位词归类到同一个字符串数组中。

```C++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        if (strs.size() == 0)    return {};
        unordered_map<string, vector<string>> m_map;
        vector<vector<string>> ans;
        for (int i = 0; i < strs.size(); ++i){
            string str_tmp = strs[i];
            std::sort(str_tmp.begin(), str_tmp.end());   //使得字母相同的按统一序列排布
            m_map[str_tmp].push_back(strs[i]) ; 
        }
        for (auto iter : m_map)
            ans.push_back(iter.second);
        
        return ans;
    }
};
```

