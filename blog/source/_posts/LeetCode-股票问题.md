---
title: LeetCode 股票问题
date: 2020-12-04 19:00:47
tags: ['LeetCode', 'DP']
categories: 题解
---

股票问题在`LeetCode`中一共有6题，也都是十分经典的`DP`问题（每题并不简单）。

<!--more-->

### [121. 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

> 给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
>
> 如果你最多只允许完成一笔交易（即买入和卖出一支股票一次），设计一个算法来计算你所能获取的最大利润。
>
> 注意：你不能在买入股票前卖出股票。
>
> **示例 1:**
>
> ```
> 输入: [7,1,5,3,6,4]
> 输出: 5
> 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 5 天（股票价格 = 6）的时候卖出，最大利润 = 6-1 = 5 。注意利润不能是 7-1 = 6, 因为卖出价格需要大于买入价格；同时，你不能在买入前卖出股票。
> ```
>
> **示例 2:**
>
> ```
> 输入: [7,6,4,3,1]
> 输出: 0
> 解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
> ```

**思路：**由于只能交易一次所以我们需要维持一个已经遍历过元素的最小值，并且通过判断当前元素与已遍历过元素最小值差值的最大值来确定买卖股票的最佳时期。

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if (prices.empty()) return 0;
        int ans = 0, cur_min = INT_MAX;
        for (int i = 0; i < prices.size(); ++i){
            if (cur_min > prices[i])    cur_min = prices[i];
            ans = max(ans, prices[i] - cur_min);
        }
        return ans;
    }
};
```

### [122. 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

> 给定一个数组，它的第 i 个元素是一支给定股票第 i 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你可以尽可能地完成更多的交易（多次买卖一支股票）。
>
> 注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
>
> **示例 1:**
>
> ```
> 输入: [7,1,5,3,6,4]
> 输出: 7
> 解释: 在第 2 天（股票价格 = 1）的时候买入，在第 3 天（股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
>      随后，在第 4 天（股票价格 = 3）的时候买入，在第 5 天（股票价格 = 6）的时候卖出, 这笔交易所能获得利润 = 6-3 = 3 。
> ```
>
> **示例 2:**
>
> ```
> 输入: [1,2,3,4,5]
> 输出: 4
> 解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。
>      注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。
>      因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
> ```
>
> **示例 3:**
>
> ```
> 输入: [7,6,4,3,1]
> 输出: 0
> 解释: 在这种情况下, 没有交易完成, 所以最大利润为 0。
> ```
>
> **提示：**
>
> - `1 <= prices.length <= 3 * 10 ^ 4`
> - `0 <= prices[i] <= 10 ^ 4`



这题我们在之前已经讨论过:[LeetCode 122](https://wuyao.work/2020/07/19/LeetCode-122-%E4%B9%B0%E5%8D%96%E8%82%A1%E7%A5%A8%E7%9A%84%E6%9C%80%E4%BD%B3%E6%97%B6%E6%9C%BAII/#more)



其实思路可以用一句话表示: 既然我们可以**通过访问数组来确定明天的股价较之今天的股价是涨还是跌**，若是涨买就肯定赚。

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if (prices.empty()) return 0;
        int ans = 0;
        for (int i = 1; i < prices.size(); ++i){
            if (prices[i - 1] < prices[i])
                ans += prices[i] - prices[i - 1];
        }
        return ans;
    }
};
```

### [123. 买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)

> 给定一个数组，它的第 i 个元素是一支给定的股票在第 i 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你最多可以完成 两笔 交易。
>
> 注意: 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
>
> **示例 1:**
>
> ```
> 输入: [3,3,5,0,0,3,1,4]
> 输出: 6
> 解释: 在第 4 天（股票价格 = 0）的时候买入，在第 6 天（股票价格 = 3）的时候卖出，这笔交易所能获得利润 = 3-0 = 3 。
>      随后，在第 7 天（股票价格 = 1）的时候买入，在第 8 天 （股票价格 = 4）的时候卖出，这笔交易所能获得利润 = 4-1 = 3 。
> 
> 
> ```
>
> **示例 2:**
>
> ```
> 输入: [1,2,3,4,5]
> 输出: 4
> 解释: 在第 1 天（股票价格 = 1）的时候买入，在第 5 天 （股票价格 = 5）的时候卖出, 这笔交易所能获得利润 = 5-1 = 4 。   
>      注意你不能在第 1 天和第 2 天接连购买股票，之后再将它们卖出。   
>      因为这样属于同时参与了多笔交易，你必须在再次购买前出售掉之前的股票。
> ```
>
> **示例 3:**
>
> ```
> 输入: [7,6,4,3,1] 
> 输出: 0 
> 解释: 在这个情况下, 没有交易完成, 所以最大利润为 0。
> ```
**思路: ** 这题是`hard`难度，是因为状态比较的多。

下面我定义了五个状态：
```C++
// dp[i][j] 表示第i天在j状态上获得的最大利润
// dp[i][0] 表示i天之前什么也没做,并且第i天也什么都没做
// dp[i][1] 表示第i天第一次买入股票
// dp[i][2] 表示第i天第一次卖出股票
// dp[i][3] 表示第i天第二次买入股票
// dp[i][4] 表示第i天第二次卖出股票
```
具体的状态转化也比较的多
```C++
dp[i][0] = 0;
dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
dp[i][2] = max(dp[i - 1][2], dp[i - 1][1] + prices[i]);
dp[i][3] = max(dp[i - 1][3], dp[i - 1][2] - prices[i]);
dp[i][4] = max(dp[i - 1][4], dp[i - 1][3] + prices[i]);
```

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if (prices.empty()) return 0;

        int size = prices.size();
        vector<vector<int>> dp(size, vector<int>(5, 0));
        for (int i = 0; i < size; ++i){
            if (i == 0){
                dp[0][1] = -prices[0];
                dp[0][3] = -prices[0];
            }
            else{
                dp[i][0] = 0;
                dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
                dp[i][2] = max(dp[i - 1][2], dp[i - 1][1] + prices[i]);
                dp[i][3] = max(dp[i - 1][3], dp[i - 1][2] - prices[i]);
                dp[i][4] = max(dp[i - 1][4], dp[i - 1][3] + prices[i]);
            }
        }
        return max(dp[size - 1][2], dp[size - 1][4]);
    }
};
```

### [188. 买卖股票的最佳时机 IV](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)

> 给定一个整数数组 prices ，它的第 i 个元素 prices[i] 是一支给定的股票在第 i 天的价格。
>
> 设计一个算法来计算你所能获取的最大利润。你最多可以完成 k 笔交易。
>
> 注意：你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
>
> **示例 1：**
>
> ```
> 输入：k = 2, prices = [2,4,1]
> 输出：2
> 解释：在第 1 天 (股票价格 = 2) 的时候买入，在第 2 天 (股票价格 = 4) 的时候卖出，这笔交易所能获得利润 = 4-2 = 2 。
> ```
>
> **示例 2：**
>
> ```
> 输入：k = 2, prices = [3,2,6,5,0,3]
> 输出：7
> 解释：在第 2 天 (股票价格 = 2) 的时候买入，在第 3 天 (股票价格 = 6) 的时候卖出, 这笔交易所能获得利润 = 6-2 = 4 。
>      随后，在第 5 天 (股票价格 = 0) 的时候买入，在第 6 天 (股票价格 = 3) 的时候卖出, 这笔交易所能获得利润 = 3-0 = 3 。
> ```
>
> **提示：**
>
> - `0 <= k <= 109`
> - `0 <= prices.length <= 1000`
> - `0 <= prices[i] <= 1000`

这一题应该是`股票系列`中最难的一题了。首先涉及到需要保存两种状态，然后还要确定当前的数所以要一个三维的数组来完整的保存状态（当然可以通过优化改进）。



**思路:** 需要三维`dp`来表示不同的状态.

````C++
// dp[i][j][0]
// i 表示当前数字 prices[i]  
// j表示第几次交易  0 <= j < k
// dp[i][j][0] 表示买入 dp[i][j][0] 表示卖出
// 状态转移方程
// dp[i][j][0] = max(dp[i-1][j][0], dp[i-1][j-1][1] - prices[i]);
// dp[i][j][1] = max(dp[i-1][j][1], dp[i-1][j][0] + prices[i]);
````

当`k`值大于`size / 2`说明可以进行无限次交易，因为一次买进一次卖出，所以当`k`值大于`size / 2`就转化为`股票问题II`了.。

```C++
class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int size = prices.size();
        if (size <= 1 || k <= 0)    return 0;
        if (k >= size / 2)  return helper(prices);
        
        vector<vector<vector<int>>> dp(size, vector<vector<int>>(k, vector<int>(2,0)));
        // 你可能有疑问 为什么不是只有dp[0][0][0] = -prices[0]就好了? 为什么把没有进行的交易都要初始化?
        for (int j = 0; j < k; ++j){
            /* 
            * 为了防止下面式子   dp[i][0][0] = max(dp[i - 1][0][0], -prices[i]);
            * 0大于负数, 再进行其他交易的时候挑选不符合题意的股票,所以要把所有交易都初始化
            */
            dp[0][j][0] = -prices[0];
            dp[0][j][1] = 0;
        }
        
        for (int i = 1; i < size; ++i){
            for (int j = 0; j < k; ++j){
                // 表示第一次交易
                if (j == 0)
                    dp[i][j][0] = max(dp[i-1][j][0], -prices[i]);
                else
                    dp[i][j][0] = max(dp[i-1][j][0], dp[i-1][j-1][1] - prices[i]);
                dp[i][j][1] = max(dp[i-1][j][1], dp[i-1][j][0] + prices[i]);
            }
        }
        return dp[size - 1][k - 1][1];
    }

    int helper(const vector<int>& prices) {
        int size = prices.size();
        if (size == 0 || size == 1)  return 0;
        int ans = 0;
        for (int i = 1; i < size; ++i) {
            if (prices[i - 1] < prices[i])
                ans += prices[i] - prices[i - 1];
        }
        return ans;
    }
};
```

### [309. 最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

> 给定一个整数数组，其中第 i 个元素代表了第 i 天的股票价格 。
>
> 设计一个算法计算出最大利润。在满足以下约束条件下，你可以尽可能地完成更多的交易（多次买卖一支股票）:
>
> - 你不能同时参与多笔交易（你必须在再次购买前出售掉之前的股票）。
> - 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。
>
> **示例:**
>
> ```
> 输入: [1,2,3,0,2]
> 输出: 3 
> 解释: 对应的交易状态为: [买入, 卖出, 冷冻期, 买入, 卖出]
> ```

**思路:**  如果理清状态转换的方式就能很好的写出代码。

![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/12/stock5.png)

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        if (prices.empty()) return 0;
        // dp[i][0] 表示冷却
        // dp[i][1] 表示买入
        // dp[i][2] 表示卖出
        int size = prices.size();
        vector<vector<int>> dp(size, vector<int>(3, 0));
        for (int i = 0; i < size; ++i){
            if (i == 0){
                dp[0][0] = 0;
                dp[0][1] = -prices[0];
            }
            else{
                dp[i][0] = max(dp[i - 1][0], dp[i - 1][2]);
                dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
                dp[i][2] = dp[i - 1][1] + prices[i];
            }
        }
        return max(dp[size - 1][0], dp[size - 1][2]);
    }
};
```



### [714. 买卖股票的最佳时机含手续费](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

> 给定一个整数数组 prices，其中第 i 个元素代表了第 i 天的股票价格 ；非负整数 fee 代表了交易股票的手续费用。
>
> 你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。
>
> 返回获得利润的最大值。
>
> 注意：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。
>
> **示例 1:**
>
> ```
> 输入: prices = [1, 3, 2, 8, 4, 9], fee = 2
> 输出: 8
> 解释: 能够达到的最大利润:  
> 在此处买入 prices[0] = 1
> 在此处卖出 prices[3] = 8
> 在此处买入 prices[4] = 4
> 在此处卖出 prices[5] = 9
> 总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8.
> ```
>
> **注意:**
>
> - `0 < prices.length <= 50000`.
> - `0 < prices[i] < 50000`.
> - `0 <= fee < 50000`.

**思路:** 这题比上一题更简单，所谓的手续费就是‘纸老虎’，状态转换图如下。
![](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/12/stock6.png)

```C++
class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        if (prices.empty()) return 0;
        // dp[i][j]
        // dp[i][0] 买入
        // dp[i][1] 卖出
        int size = prices.size();
        vector<vector<int>> dp(size, vector<int>(2, 0));
        for (int i = 0; i < size; ++i){
            if (i == 0)
                dp[0][0] = -prices[i];
            else{
                dp[i][0] = max(dp[i - 1][0], dp[i - 1][1] - prices[i]);
                dp[i][1] = max(dp[i - 1][1], dp[i - 1][0] + prices[i] - fee);
            }
        }
        return max(dp[size - 1][0], dp[size - 1][1]);
    }
};
```

