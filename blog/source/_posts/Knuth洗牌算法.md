---
title: Knuth洗牌算法
date: 2020-08-13 23:45:28
tags: ['智力题', 'LeetCode']
categories: ['算法', '题解']
---

记录下今天所学习有意思的洗牌算法.

<!--more-->

#### 如何设计出一个公平的洗牌算法?

给定`n`张牌，如何去设计出一个公平的洗牌算法？

想到洗牌算法肯定是跟随机算法有关，我们可以很容易想到在`n`个数中任取`k`次，每次取两张牌，并且把两张牌交换顺序，放回原来的牌组中去。

这种想法没错，但也很暴力。最主要的原因是没有达到公平这一点的要求。

我们知道`n`张牌，并且每张牌都不同，进行全排列有`n!`种组合.

我们可以从`n!`组合中 **任取一种** ，也能达到上面所说的要求，尤其是实现了公平这一定义。

但是我们知道生成`n!`种组合时间复杂度起码是在`O(n!)`的级别了，这一级别的时间复杂度不能被称为一个完美的算法。

#### Knuth洗牌算法

首先我们来说明下公平这个概念在洗牌中的意思:**对于生成的排列，每个元素都能够等概率地出现在每一个位置。**

`knuth`洗牌算法的核心是:**每次从数组中随机选出一个数，然后与最后一个数交换位置， 并且不再考虑最后一个数。**

具体解释可以看看 [吴师兄]( https://www.bilibili.com/video/BV1k7411q7jo )

`LeetCode`中也有对应的题目:[384. 打乱数组](https://leetcode-cn.com/problems/shuffle-an-array/)

 打乱一个没有重复元素的数组。 

>  **示例:** 
>
> ```
> // 以数字集合 1, 2 和 3 初始化数组。
> int[] nums = {1,2,3};
> Solution solution = new Solution(nums);
> 
> // 打乱数组 [1,2,3] 并返回结果。任何 [1,2,3]的排列返回的概率应该相同。
> solution.shuffle();
> 
> // 重设数组到它的初始状态[1,2,3]。
> solution.reset();
> 
> // 随机返回数组[1,2,3]打乱后的结果。
> solution.shuffle();
> ```
>
> 

```C++
class Solution {
public:
    Solution(vector<int>& nums) {
        _poker = nums;
        _repoker = nums;
    }
    
    /** Resets the array to its original configuration and return it. */
    vector<int> reset() {
        _poker = _repoker;
        return _poker;
    }
    
    /** Returns a random shuffling of the array. */
    vector<int> shuffle() {
        for (int i = _poker.size() - 1; i >= 0; --i){
            int pos = random() % (i + 1);  // 随机生成 下标在 [1 - i] 之间的数
            swap(_poker[pos], _poker[i]);  // 交换随机生成的下标和当前最后一个元素的值
        }
        return _poker;
    }

    vector<int> _poker;
    vector<int> _repoker;
};
```

