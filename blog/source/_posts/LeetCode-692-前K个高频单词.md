---
title: LeetCode-692.前K个高频单词
date: 2020-04-19 22:50:20
tags: ['LeetCode', 'Priority_queue']
categories: 题解
---

知识面较为广的一个题，看到前K个XXX，前面的博文我就说过没思路就排序，看到这么明显的提示说明大概率可以使用堆来解决该问题。

<!--more-->

#### [692. 前K个高频单词](https://leetcode-cn.com/problems/top-k-frequent-words/)

> 给一非空的单词列表，返回前 k 个出现次数最多的单词。
>
> 返回的答案应该按单词出现频率由高到低排序。如果不同的单词有相同出现频率，按字母顺序排序。
>
> **示例 1：**
>
> ```
> 输入: ["i", "love", "leetcode", "i", "love", "coding"], k = 2
> 输出: ["i", "love"]
> 解析: "i" 和 "love" 为出现次数最多的两个单词，均为2次。
>     注意，按字母顺序 "i" 在 "love" 之前。
> ```
>
> **示例 2：**
>
> ```
> 输入: ["the", "day", "is", "sunny", "the", "the", "the", "sunny", "is", "is"], k = 4
> 输出: ["the", "is", "sunny", "day"]
> 解析: "the", "is", "sunny" 和 "day" 是出现次数最多的四个单词，
>     出现次数依次为 4, 3, 2 和 1 次。
> ```
>
> **注意：**
>
> 假定 k 总为有效值， 1 ≤ k ≤ 集合元素数。
> 输入的单词均由小写字母组成。
>
> **扩展练习：**
>
> 尝试以 O(n log k) 时间复杂度和 O(n) 空间复杂度解决。
>

### 较为容易想到的O(nlogn)的方法
1. 先将words中的每个string出现的次数存入hash_table中进行统计
2. 遍历hash_table，将所以的元素存入vector中再进行排序
3. 取前K大的
4. 由于需要排序故时间复杂度为`O(nlogn)`
```C++
class Solution {
public:
    vector<string> topKFrequent(vector<string>& words, int k) {
        unordered_map<string, int> m_map;
        for (int i = 0; i < words.size(); ++i)
            m_map[words[i]]++;
        vector<pair<string, int>> vec;
        for (auto iter : m_map)
            vec.push_back({iter.first, iter.second});
        auto comp = [](pair<string, int>a, pair<string, int>b){
         	//stirng 中的 < 是根据字典顺序比较的
            if (a.second == b.second) return a.first < b.first; 
            return a.second > b.second;
        };
        sort(vec.begin(), vec.end(), comp);
        vector<string> ans;
        for (int i = 0; i < k; ++i)
            ans.push_back(vec[i].first);
        return ans;
    }
};
```

### O(nlogk)的方法

和上述方法很接近，只是不需要通过排序的方法来获取前K个而是通过`priority_queue`来获得，又由于堆的插入是`log(n)`，其中n为堆的大小。由于我们只需要维系一个K个大小的堆故 ==> 时间复杂度为`O(nlogk)`。

这里有个需要注意的点就是对堆的理解，不要背诵大顶堆小顶堆这种概念，是没意义的。

大顶堆在比较函数那一个参数传的参数是`less<int>`，这里有个简单的理解就是把堆看成一个数组，这个数组是递增的。

小顶堆在比较函数那一个参数传的参数是`greate<int>`， 这个数组是递减的。

我们下面代码是根据我们自定义的顺序进行排列的，在使用的时候只需要把它想象成一个线性的数组会简化思考的复杂度。

```C++
class Solution {
public:
    vector<string> topKFrequent(vector<string>& words, int k) {
        unordered_map<string, int> m_map;
        for (int i = 0; i < words.size(); ++i)
            m_map[words[i]]++;
        auto comp = [](pair<string, int>a, pair<string, int>b){
            if (a.second == b.second)   return a.first < b.first;
            return a.second > b.second;
        };
        priority_queue<pair<string, int>, vector<pair<string, int>>, decltype(comp)> q(comp);
        for (auto iter : m_map){
            q.push({iter.first, iter.second});
            if (q.size() > k)
                q.pop();
        }
        vector<string> ans;
        while (!q.empty()){
            ans.push_back(q.top().first);
            q.pop();
        }
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

