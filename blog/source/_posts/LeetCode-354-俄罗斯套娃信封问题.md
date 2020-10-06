---
title: LeetCode 354.俄罗斯套娃信封问题
date: 2020-10-06 15:02:48
tags: ['LeetCode', 'DP']
categories: ['题解']
---

二维的`LIS`问题

可以先看经典的[LIS](https://lavafoxyao.github.io/2020/07/26/LeetCode-300-%E6%9C%80%E9%95%BF%E4%B8%8A%E5%8D%87%E5%AD%90%E5%BA%8F%E5%88%97/#more)

<!--more-->

#### [354. 俄罗斯套娃信封问题](https://leetcode-cn.com/problems/russian-doll-envelopes/)

> 给定一些标记了宽度和高度的信封，宽度和高度以整数对形式 (w, h) 出现。当另一个信封的宽度和高度都比这个信封大的时候，这个信封就可以放进另一个信封里，如同俄罗斯套娃一样。
>
> 请计算最多能有多少个信封能组成一组“俄罗斯套娃”信封（即可以把一个信封放到另一个信封里面）。
>
>  **说明:**
> 不允许旋转信封。 
>
>  **示例:** 
>
> ```
> 输入: envelopes = [[5,4],[6,4],[6,7],[2,3]]
> 输出: 3 
> 解释: 最多信封的个数为 3, 组合为: [2,3] => [5,4] => [6,7]。
> ```



思路：将信封按宽度和高度从小到大排序，然后就转化成一维的LIS问题了

```C++
class Solution {
public:
    int maxEnvelopes(vector<vector<int>>& envelopes) {
        if (envelopes.empty())  return 0;
        int size = envelopes.size();
        // 按宽 高 从小到大排序
        sort(envelopes.begin(), envelopes.end(), 
        [](vector<int> A, vector<int> B){
            if (A[0] == B[0])   return A[1] < B[1];
            return A[0] < B[0];
        });
        // dp[i] 表示以envelopes[i]结尾的最多的信封数
        // dp[i] = max{dp[j]} + 1  其中 j < i
        // && envelops[j][0] < envelops[i][0] 
        // && envelops[j][1] < envelops[i][1]
        vector<int> dp(size, 0);
        int ans = 1;
        for (int i = 0; i < size; ++i){
            int cur_max = 0;
            for (int j = 0; j < i; ++j){
                if (envelopes[j][0] < envelopes[i][0] && 
                envelopes[j][1] < envelopes[i][1] && cur_max < dp[j])
                    cur_max = dp[j];
            }
            dp[i] = cur_max + 1;
            ans = max(ans, dp[i]);
        }
        return ans;
    }
};
```

