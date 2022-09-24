---
title: JS算法：动态规划
date: 2022-09-24 20:00:24
tags:
  - 算法
categories:
  - 算法
keywords:
  - 算法
swiper_index: 7
swiper_desc: 动态规划
swiper_cover: /img/algorithm/DP.jpg
top_img: /img/algorithm/DP.jpg
cover: /img/algorithm/DP.jpg
---

# 动态规划

## 动态规划理论基础

>动态规划（dynamic programming，DP）是一种将复杂问题分解成更小的子问题来解决的优化技术。
>
>动态规划和分而治之是不同的方法。分而治之方法是把问题分解成相互独立的子问题，然后组合它们的答案，而动态规划则是将问题分解成相互依赖的子问题。

**用动态规划解决问题时，要遵循五个重要步骤：**

- 确定dp数组（dp table）以及下标的含义

- 确定递推公式（状态转移公式）

- dp数组如何初始化

- 确定遍历顺序
- 举例推导dp数组

## 动态规划实例

**1、leetcode 509.斐波那契数**  https://leetcode.cn/problems/fibonacci-number/

![](/img/algorithm/leetcode509.png)

**解题思路**

- dp[i]的定义为：第i个数的斐波那契数值是dp[i]；
- dp[i] = dp[i - 1] + dp[i - 2]；
- dp[0] = 0，dp[1] = 1;
- 从递推公式dp[i] = dp[i - 1] + dp[i - 2];中可以看出，dp[i]是依赖 dp[i - 1] 和 dp[i - 2]，那么遍历的顺序一定是从前到后遍历的；
- 按照这个递推公式 dp[i] = dp[i - 1] + dp[i - 2]，推导一下，当N为10的时候，dp数组应该是：0 1 1 2 3 5 8 13 21 34 55。

**代码实现**

```javascript
var fib = function(n) {
    let dp = [0, 1];
    for(let i = 2; i <= n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    console.log(dp);
    return dp[n];
};
```

**2、leetcode 70.爬楼梯**  https://leetcode.cn/problems/climbing-stairs/

![](/img/algorithm/leetcode70.png)

**解题思路**

- dp[i]： 爬到第i层楼梯，有dp[i]种方法；
- dp[i] = dp[i - 1] + dp[i - 2] ；
- dp[1] = 1，dp[2] = 2；
- 从递推公式dp[i] = dp[i - 1] + dp[i - 2]；中可以看出，遍历顺序一定是从前向后遍历的。

**代码实现**

```javascript
var climbStairs = function (n) {
    let dp = [1, 2];
    for (let i = 2; i < n; i++) {
        dp[i] = dp[i - 1] + dp[i - 2];
    }
    return dp[n - 1];
};
```

**3、leetcode 746.使用最小花费爬楼梯**  https://leetcode.cn/problems/min-cost-climbing-stairs/

![](/img/algorithm/leetcode746.png)

**解题思路**

- dp[i]： 到达第i个台阶所花费的最少体力为dp[i]；
- dp[i] = min(dp[i - 1]，dp[i - 2]) + cost[i]；
- dp[0] = cost[0]，dp[1] = cost[1]；
- 从递推公式dp[i - 1]，dp[i - 2]中可以看出，遍历顺序一定是从前向后遍历的。

**代码实现**

```javascript
var minCostClimbingStairs = function (cost) {
    const dp = [cost[0], cost[1]];
    for (let i = 2; i < cost.length; i++) {
        dp[i] = Math.min(dp[i - 1] + cost[i], dp[i - 2] + cost[i]);
    }
    return Math.min(dp[cost.length - 1], dp[cost.length - 2]);
};
```

**4、leetcode 62.不同路径**  https://leetcode.cn/problems/unique-paths/

![](/img/algorithm/leetcode62.png)

**解题思路**

- dp[i] [j] ：表示从（0 ，0）出发，到(i, j) 有dp[i] [j]条不同的路径；

- dp[i] [j] = dp[i-1] [j] + dp[i] [j-1]；

- 
```javascript
  for (int i = 0; i < m; i++) dp[i][0] = 1;
  for (int j = 0; j < n; j++) dp[0][j] = 1;
  ```

- 从递推公式dp[i] [j] = dp[i-1] [j] + dp[i] [j-1]中可以看出，遍历顺序一定是从左向右遍历的。

**代码实现**

```javascript
var uniquePaths = function (m, n) {
    const dp = Array(m).fill().map(item => Array(n));
    for (let i = 0; i < m; ++i) {
        dp[i][0] = 1;
    }

    for (let i = 0; i < n; ++i) {
        dp[0][i] = 1;
    }

    for (let i = 1; i < m; ++i) {
        for (let j = 1; j < n; ++j) {
            dp[i][j] = dp[i - 1][j] + dp[i][j - 1];
        }
    }
    return dp[m - 1][n - 1];
};
```

