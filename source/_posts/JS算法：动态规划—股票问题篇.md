---
title: JS算法：动态规划—股票问题篇
date: 2022-09-26 16:45:41
tags:
  - 算法
categories:
  - 算法
keywords:
  - 算法
abbrlink: 动态规划—股票问题篇
top_img: /img/algorithm/DP/DP.jpg
cover: /img/algorithm/DP/DP.jpg
---

# 股票问题篇

**1、leetcode 121.买卖股票的最佳时机**  https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/

![](/img/algorithm/DP/leetcode121.png)

**解题思路**

- dp[i][0] 表示第i天持有股票所得最多现金，dp[i][1] 表示第i天不持有股票所得最多现金；

- 如果第i天持有股票即dp[i][0]， 那么可以由两个状态推出来：

  - 第i-1天就持有股票，那么就保持现状，所得现金就是昨天持有股票的所得现金 即：dp[i - 1][0]
  - 第i天买入股票，所得现金就是买入今天的股票后所得现金即：-prices[i]

  那么dp[i][0]应该选所得现金最大的，所以dp[i][0] = max(dp[i - 1][0], -prices[i]);

  如果第i天不持有股票即dp[i][1]， 也可以由两个状态推出来：

  - 第i-1天就不持有股票，那么就保持现状，所得现金就是昨天不持有股票的所得现金 即：dp[i - 1][1]
  - 第i天卖出股票，所得现金就是按照今天股票佳价格卖出后所得现金即：prices[i] + dp[i - 1][0]

  同样dp[i][1]取最大的，dp[i][1] = max(dp[i - 1][1], prices[i] + dp[i - 1][0]);

- dp[0][0]表示第0天持有股票，此时的持有股票就一定是买入股票了，因为不可能有前一天推出来，所以dp[0][0] -= prices[0];

  dp[0][1]表示第0天不持有股票，不持有股票那么现金就是0，所以dp[0][1] = 0;

- 遍历顺序：从前向后；

**代码实现**

```javascript
var maxProfit = function (prices) {
    const len = prices.length;
    // 创建dp数组
    const dp = new Array(len).fill([0, 0]);
    // dp数组初始化
    dp[0] = [-prices[0], 0];
    for (let i = 1; i < len; i++) {
        // 更新dp[i]
        dp[i] = [
            Math.max(dp[i - 1][0], -prices[i]),
            Math.max(dp[i - 1][1], prices[i] + dp[i - 1][0]),
        ];
    }
    return dp[len - 1][1];
};
```

**2、leetcode 122.买卖股票的最佳时机 II**  https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/

![](/img/algorithm/DP/leetcode122.png)

**解题思路**

- dp[0][0]表示第0天持有股票，此时的持有股票就一定是买入股票了，因为不可能有前一天推出来，所以dp[0][0] -= prices[0];

  dp[0][1]表示第0天不持有股票，不持有股票那么现金就是0，所以dp[0][1] = 0;

- 遍历顺序：从前向后；

**代码实现**

```javascript
// 方法一：动态规划（dp 数组）
const maxProfit = (prices) => {
    let dp = Array.from(Array(prices.length), () => Array(2).fill(0));
    // dp[i][0] 表示第i天持有股票所得现金。
    // dp[i][1] 表示第i天不持有股票所得最多现金
    dp[0][0] = 0 - prices[0];
    dp[0][1] = 0;
    for(let i = 1; i < prices.length; i++) {
        // 如果第i天持有股票即dp[i][0]， 那么可以由两个状态推出来
        // 第i-1天就持有股票，那么就保持现状，所得现金就是昨天持有股票的所得现金 即：dp[i - 1][0]
        // 第i天买入股票，所得现金就是昨天不持有股票的所得现金减去 今天的股票价格 即：dp[i - 1][1] - prices[i]
        dp[i][0] = Math.max(dp[i-1][0], dp[i-1][1] - prices[i]);
        
        // 在来看看如果第i天不持有股票即dp[i][1]的情况， 依然可以由两个状态推出来
        // 第i-1天就不持有股票，那么就保持现状，所得现金就是昨天不持有股票的所得现金 即：dp[i - 1][1]
        // 第i天卖出股票，所得现金就是按照今天股票佳价格卖出后所得现金即：prices[i] + dp[i - 1][0]
        dp[i][1] = Math.max(dp[i-1][1], dp[i-1][0] + prices[i]);
    }

    return dp[prices.length -1][1];
};

// 方法二：动态规划（滚动数组）
const maxProfit = (prices) => {
    // 滚动数组
    // have: 第i天持有股票最大收益; notHave: 第i天不持有股票最大收益
    let n = prices.length,
        have = -prices[0],
        notHave = 0;
    for (let i = 1; i < n; i++) {
        have = Math.max(have, notHave - prices[i]);
        notHave = Math.max(notHave, have + prices[i]);
    }
    // 最终手里不持有股票才能保证收益最大化
    return notHave;
}
```

**3、leetcode 123.买卖股票的最佳时机 III**  https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iii/

![](/img/algorithm/DP/leetcode123.png)

**解题思路**

- 关键在于至多买卖两次，这意味着可以买卖一次，可以买卖两次，也可以不买卖；

  一天一共就有五个状态：

  - 没有操作
  - 第一次买入
  - 第一次卖出
  - 第二次买入
  - 第二次卖出
  - dp[i][j]中 i表示第i天，j为 [0 - 4] 五个状态，dp[i][j]表示第i天状态j所剩最大现金；

- 达到dp[i][1]状态，有两个具体操作：

  - 操作一：第i天买入股票了，那么dp[i][1] = dp[i-1][0] - prices[i]
  - 操作二：第i天没有操作，而是沿用前一天买入的状态，即：dp[i][1] = dp[i - 1][1]

  选最大的，所以 dp[i][1] = max(dp[i-1][0] - prices[i], dp[i - 1][1])；

  dp[i][2]也有两个操作：

  - 操作一：第i天卖出股票了，那么dp[i][2] = dp[i - 1][1] + prices[i]
  - 操作二：第i天没有操作，沿用前一天卖出股票的状态，即：dp[i][2] = dp[i - 1][2]

  选最大的，所以dp[i][2] = max(dp[i - 1][1] + prices[i], dp[i - 1][2])

  同理可推出剩下状态部分：

  dp[i][3] = max(dp[i - 1][3], dp[i - 1][2] - prices[i])；

  dp[i][4] = max(dp[i - 1][4], dp[i - 1][3] + prices[i])；

- dp数组如何初始化

  - 第0天没有操作：dp[0][0] = 0；
  - 第0天第一次买入的操作：dp[0][1] = -prices[0]；
  - 第0天第一次卖出的操作：dp[0][2] = 0；
  - 第0天第二次买入的操作：dp[0][3] = -prices[0]；
  - 第0天第二次卖出的操作：dp[0][4] = 0；

- 遍历顺序：从前向后；

**代码实现**

```javascript
//方法一
var maxProfit = function (prices) {
    const len = prices.length;
    const dp = new Array(len).fill(0).map(x => new Array(5).fill(0));
    dp[0][1] = -prices[0];
    dp[0][3] = -prices[0];
    for (let i = 1; i < len; i++) {
        dp[i][0] = dp[i - 1][0];
        dp[i][1] = Math.max(dp[i - 1][1], dp[i - 1][0] - prices[i]);
        dp[i][2] = Math.max(dp[i - 1][2], dp[i - 1][1] + prices[i]);
        dp[i][3] = Math.max(dp[i - 1][3], dp[i - 1][2] - prices[i]);
        dp[i][4] = Math.max(dp[i - 1][4], dp[i - 1][3] + prices[i]);
    }
    return dp[len - 1][4];
};

// 方法二
var maxProfit = function (prices) {
    const len = prices.length;
    const dp = new Array(5).fill(0);
    dp[1] = -prices[0];
    dp[3] = -prices[0];
    for (let i = 1; i < len; i++) {
        dp[1] = Math.max(dp[1], dp[0] - prices[i]);
        dp[2] = Math.max(dp[2], dp[1] + prices[i]);
        dp[3] = Math.max(dp[3], dp[2] - prices[i]);
        dp[4] = Math.max(dp[4], dp[3] + prices[i]);
    }
    return dp[4];
};
```

**4、leetcode 188.买卖股票的最佳时机 IV**  https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-iv/

![](/img/algorithm/DP/leetcode188.png)

**解题思路**

- 使用二维数组 dp[i][j] ：第i天的状态为j，所剩下的最大现金是dp[i][j]

  j的状态表示为：

  - 0 表示不操作
  - 1 第一次买入
  - 2 第一次卖出
  - 3 第二次买入
  - 4 第二次卖出
  - **除了0以外，偶数就是卖出，奇数就是买入**
  - 至多有K笔交易，那么j的范围就定义为 2 * k + 1

**代码实现**

```javascript
var maxProfit = (k,prices) => {
    if (prices == null || prices.length < 2 || k == 0) {
        return 0;
    }
    
    let dp = Array.from(Array(prices.length), () => Array(2*k+1).fill(0));

    for (let j = 1; j < 2 * k; j += 2) {
        dp[0][j] = 0 - prices[0];
    }
    
    for(let i = 1; i < prices.length; i++) {
        for (let j = 0; j < 2 * k; j += 2) {
            dp[i][j+1] = Math.max(dp[i-1][j+1], dp[i-1][j] - prices[i]);
            dp[i][j+2] = Math.max(dp[i-1][j+2], dp[i-1][j+1] + prices[i]);
        }
    }

    return dp[prices.length - 1][2 * k];
};
```