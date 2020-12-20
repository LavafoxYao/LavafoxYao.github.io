---
title: LeetCode 316.去除重复字母
date: 2020-12-20 15:03:30
tags: ['LeetCode', '贪心']
categories: ['题解']
---

`2020/12/20`的每日一题，是一道很有意思的贪心题，值得一做。



每日一题出的题目质量还是挺高的，每天还是要坚持做一做呀！

<!--more-->

 [316. 去除重复字母](https://leetcode-cn.com/problems/remove-duplicate-letters/)

>  给你一个字符串 `s` ，请你去除字符串中重复的字母，使得每个字母只出现一次。需保证 **返回结果的字典序最小**（要求不能打乱其他字符的相对位置）。 
>
> **示例 1：**
>
> ```
> 输入：s = "bcabc"
> 输出："abc"
> ```
>
> **示例 2：**
>
> ```
> 输入：s = "cbacdcbc"
> 输出："acdb"
> ```
>
> **提示：**
>
> - `1 <= s.length <= 10^4`
> - `s` 由小写英文字母组成

思路就是: 遍历字符串s，若当前字符c比答案的串`ans`，最后一个字符要小，并且`ans`串最后一个字符还会出现在下面的遍历中，那么就可以去除掉。



我们把**示例1**用上述思想用图的方式解释一遍，大概的过程就如下图：

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/%E7%AC%94%E8%AE%B0/2020/12/leetcode316.png)

看起来好像是对的，其实如果给的示例是："abacb"  上述方法得到的答案是 "acb"，而正确答案是**“abc”**。



我们的想法究竟哪里不对呢？噢，原来是已经存在于答案串中的字符，再次遇到无需处理。



这样我们就得到完整的算法了：**遍历字符串s，若当前字符已经存在于答案串`ans`中则直接跳过。若当前字符c比答案的串`ans`，最后一个字符要小，并且`ans`串最后一个字符还会出现在下面的遍历中，那么就可以去除掉。**



```C++
class Solution {
public:
    string removeDuplicateLetters(string s) {
        if (s.empty())  return " ";
        // 记录所有的字符的个数,为下面判断剩余的串是否还存在该字符做了准备
        vector<int> ch_map(26, 0);
        // 记录答案串中已经出现的字符
        vector<bool> visited_ch(26, false);
        string ans;
        for (auto c : s)    ch_map[c - 'a'] ++;

        for (auto c : s){
            // 剩下的串已经少了一个c所以要-1
            ch_map[c - 'a']--;
            // 判断当前字符是否出现在答案串中,若存在直接跳过
            if (visited_ch[c - 'a'])    continue;
            while (!ans.empty() && c < ans.back() && ch_map[ans.back() - 'a'] > 0){
                visited_ch[ans.back() - 'a'] = false;
                ans.pop_back();
            }
            ans.push_back(c);
            visited_ch[c - 'a'] = true;
        }
        return ans;
    }
};
```

