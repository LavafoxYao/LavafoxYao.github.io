---
title: LeetCode 820.单词的压缩编码
date: 2020-07-26 23:42:53
tags: ['leetCode']
categories: 题解
---

#### [820. 单词的压缩编码](https://leetcode-cn.com/problems/short-encoding-of-words/)

<!--more-->

> 给定一个单词列表，我们将这个列表编码成一个索引字符串 S 与一个索引列表 A。
>
> 例如，如果这个列表是 ["time", "me", "bell"]，我们就可以将其表示为 S = "time#bell#" 和 indexes = [0, 2, 5]。
>
> 对于每一个索引，我们可以通过从字符串 S 中索引的位置开始读取字符串，直到 "#" 结束，来恢复我们之前的单词列表。
>
> 那么成功对给定单词列表进行编码的最小字符串长度是多少呢？
>
> **示例：**
>
> ```
> 输入: words = ["time", "me", "bell"]
> 输出: 10
> 说明: S = "time#bell#" ， indexes = [0, 2, 5] 。
> ```
>
> **提示：**
>
> 1. `1 <= words.length <= 2000`
> 2. `1 <= words[i].length <= 7`
> 3. 每个单词都是小写字母 。

思路: 参考[nettee]( https://leetcode-cn.com/problems/short-encoding-of-words/solution/wu-xu-zi-dian-shu-qing-qing-yi-fan-zhuan-jie-guo-j/ )的题解.

采用`反转 + 排序`的方法,其中字符串排序在`STL`中默认的是`字典序`

**字典序:  设想一本英语字典里的单词，哪个在前哪个在后？ 显然的做法是先按照第一个字母、以 a、b、c……z 的顺序排列；如果第一个字母一样，那么比较第二个、第三个乃至后面的字母。如果比到最后两个单词不一样长（比如，sigh 和 sight），那么把短者排在前。 **

```C++
class Solution {
public:
    int minimumLengthEncoding(vector<string>& words) {
        if (words.empty())  return 0;
        // 因为后缀相同的会被归结到同一个单词,并作出标记
        // 我们可以把后缀==>前缀问题
        for (auto& word : words)
            reverse(word.begin(), word.end());
        // 字典序, 将前序相同的都放在了一起并且按照长度从小到大排列
        sort(words.begin(), words.end());
        int ans = 0;
        for (int i = 0; i < words.size() - 1; ++i){
            int size = words[i].size();
            if (words[i] == words[i + 1].substr(0, size))
                continue;
            // 只有没有压缩的前序需要加#进行区分
            ans += (size + 1);  
        }
        // 由于最后一个字符串在同字典序中最长,肯定不能成为其他字符串的前序
        return ans + words.back().size() + 1;
    }
};
```

