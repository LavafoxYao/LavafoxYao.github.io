---
title: LeetCode-127.单词接龙
date: 2020-04-04 21:24:19
tags: LeetCode
categories: 题解
---

很有意思的一道`BFS`的搜索题。

<!--more-->

#### [127. 单词接龙](https://leetcode-cn.com/problems/word-ladder/)

> 给定两个单词（beginWord 和 endWord）和一个字典，找到从 beginWord 到 endWord 的最短转换序列的长度。转换需遵循如下规则：
>
> 1. 每次转换只能改变一个字母。
>
> 2. 转换过程中的中间单词必须是字典中的单词。
>
> 说明:
>
> - 如果不存在这样的转换序列，返回 0。
> - 所有单词具有相同的长度。
> - 所有单词只由小写字母组成。
> - 字典中不存在重复的单词。
> - 你可以假设 beginWord 和 endWord 是非空的，且二者不相同。
>
> **示例 1:**
>
> ```
> 输入:
> beginWord = "hit",
> endWord = "cog",
> wordList = ["hot","dot","dog","lot","log","cog"]
> 
> 输出: 5
> 
> 解释: 一个最短转换序列是 "hit" -> "hot" -> "dot" -> "dog" -> "cog",
>   返回它的长度 5。
> ```
>
> **示例 2:**
>
> ```
> 输入:
> beginWord = "hit"
> endWord = "cog"
> wordList = ["hot","dot","dog","lot","log"]
> 
> 输出: 0
> 
> 解释: endWord "cog" 不在字典中，所以无法进行转换。
> ```

刚开始做本题的时候，并没有思路，就到`bilibili`找了下题解的视频。[花花酱](<https://www.bilibili.com/video/BV1yt411Y7Me?from=search&seid=9474205041239655571>)

花花讲的还是十分通俗易懂的，详细的解法请移步花花的视频.

下面我将实现下`BFS`

```C++
class Solution {
public:
    int ladderLength(string beginWord, string endWord, vector<string>& wordList) {
        unordered_set<string> wordSet(wordList.begin(), wordList.end());
        if (!wordSet.count(endWord)) return 0;
        int ans = 0;
        int wordLen = beginWord.size();
        queue<string> q;
        q.push(beginWord);
        while (!q.empty()){
            ans++;
            int queSize = q.size();
            for (int i = 0; i < queSize; ++i){
                string word = q.front();
                q.pop();
                for (int j = 0; j < wordLen; ++j){
                    char ch = word[j];
                    for (int k = 'a'; k <= 'z'; ++k){
                        word[j] = k;
                        if (word == endWord) return ans + 1; 
                        if (!wordSet.count(word)) continue; 
                        q.push(word);
                        wordSet.erase(word);                //删除已经遍历过的单词(找到最短的换序列的长度)
                    } 
                    word[j] = ch;
                }
            }
        }
        return 0;
    }
};
```

