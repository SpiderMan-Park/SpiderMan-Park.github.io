---
title: JS算法：动态规划—背包问题篇
date: 2022-09-26 16:45:13
tags:
  - 算法
categories:
  - 算法
keywords:
  - 算法
abbrlink: 动态规划—背包问题篇
top_img: /img/algorithm/DP/DP.jpg
cover: /img/algorithm/DP/DP.jpg
---

# 动态规划—背包问题

## 01 背包理论基础

> **标准背包问题：**
>
> 有 n 件物品和一个最多能背重量为 w 的背包。第 i 件物品的重量是 weight[i]，得到的价值是 value[i] 。**每件物品只能用一次**，求解将哪些物品装入背包里物品价值总和最大。

![](/img/algorithm/DP/bag.jpg)

**例：**

背包最大重量为 4。

物品为：

<table style="width:23%">
   <tr>  <th></th>   <th>重量</th> <th>价值</th>  </tr>
   <tr>  <td>物品0</td> <td>1</td> <td>15</td>  </tr>
   <tr>  <td>物品1</td> <td>3</td> <td>20</td>  </tr>
   <tr>  <td>物品2</td> <td>4</td> <td>30</td>  </tr>
</table>

问：背包能背的物品最大价值是多少？

### 二维 dp 数组 01 背包

- **确定 dp 数组（dp table）以及下标的含义**

  - 对于背包问题，有一种写法， 是使用二维数组，即**dp[i][j] 表示从下标为[0-i]的物品里任意取，放进容量为 j 的背包，价值总和最大是多少**。
    ![](/img/algorithm/DP/bag1.png)

- **确定递推公式（状态转移公式）**

  - **不放物品 i**：由 dp[i - 1][j]推出，即背包容量为 j，里面不放物品 i 的最大价值，此时 dp[i][j]就是 dp[i - 1][j]。(其实就是当物品 i 的重量大于背包 j 的重量时，物品 i 无法放进背包中，所以被背包内的价值依然和前面相同。)
  - **放物品 i**：由 dp[i - 1]j - weight[i]]推出，dp[i - 1]j - weight[i]] 为背包容量为 j - weight[i]的时候不放物品 i 的最大价值，那么 dp[i - 1]j - weight[i]] + value[i] （物品 i 的价值），就是背包放物品 i 得到的最大价值
  - **所以递推公式：** dp[i][j] = max(dp[i - 1][j], dp[i - 1]j - weight[i]] + value[i])；

- **dp 数组如何初始化**

  - 如果背包容量 j 为 0 的话，即 dp[i][0]，无论是选取哪些物品，背包价值总和一定为 0。

    ![](/img/algorithm/DP/bag2.png)

  - 状态转移方程 dp[i][j] = max(dp[i - 1][j], dp[i - 1]j - weight[i]] + value[i]); 可以看出 i 是由 i-1 推导出来，那么 i 为 0 的时候就一定要初始化。

  - dp[0][j]，即：i 为 0，存放编号 0 的物品的时候，各个容量的背包所能存放的最大价值。

  - 那么很明显当 j < weight[0]的时候，dp[0][j] 应该是 0，因为背包容量比编号 0 的物品重量还小。

  - 当 j >= weight[0]时，dp[0][j] 应该是 value[0]，因为背包容量放足够放编号 0 物品。

    ![](/img/algorithm/DP/bag3.png)

  代码初始化如下：

  ```javascript
  for (let j = 0; j < weight[0]; j++) {
    // 当然这一步，如果把dp数组预先初始化为0了，这一步就可以省略
    dp[0][j] = 0;
  }
  // 正序遍历
  for (let j = weight[0]; j <= bagweight; j++) {
    dp[0][j] = value[0];
  }
  ```

- **确定遍历顺序**
  - 先遍历物品再遍历背包重量

**完整实现代码：**

```javascript
function testWeightBagProblem(weight, value, size) {
  // 定义 dp 数组
  const len = weight.length,
    dp = Array(len)
      .fill()
      .map(() => Array(size + 1).fill(0));

  // 初始化
  for (let j = weight[0]; j <= size; j++) {
    dp[0][j] = value[0];
  }

  // weight 数组的长度len 就是物品个数
  for (let i = 1; i < len; i++) {
    // 遍历物品
    for (let j = 0; j <= size; j++) {
      // 遍历背包容量
      if (j < weight[i]) dp[i][j] = dp[i - 1][j];
      else
        dp[i][j] = Math.max(dp[i - 1][j], dp[i - 1][j - weight[i]] + value[i]);
    }
  }
  console.table(dp);
  return dp[len - 1][size];
}

function test() {
  console.log(testWeightBagProblem([1, 3, 4, 5], [15, 20, 30, 55], 6));
}

test();
```

### 一维 dp 数组（滚动数组）

对于背包问题其实状态都是可以压缩的。

在使用二维数组的时候，递推公式：dp[i][j] = max(dp[i - 1][j], dp[i - 1]j - weight[i]] + value[i]);

**其实可以发现如果把 dp[i - 1]那一层拷贝到 dp[i]上，表达式完全可以是：dp[i][j] = max(dp[i][j], dp[i]j - weight[i]] + value[i]);**

**与其把 dp[i - 1]这一层拷贝到 dp[i]上，不如只用一个一维数组了**，只用 dp[j]（一维数组，也可以理解是一个滚动数组）。

这就是滚动数组的由来，需要满足的条件是上一层可以重复利用，直接拷贝到当前层。

**dp[i][j] 表示从下标为[0-i]的物品里任意取，放进容量为 j 的背包，价值总和最大是多少**。

- 确定 dp 数组（dp table）以及下标的含义

  - 在一维 dp 数组中，dp[j]表示：容量为 j 的背包，所背的物品价值可以最大为 dp[j]。

- 确定递推公式（状态转移公式）

  - dp[j]为 容量为 j 的背包所背的最大价值；

  - dp[j]可以通过 dp[j - weight[i]]推导出来，dp[j - weight[i]]表示容量为 j - weight[i]的背包所背的最大价值；

  - dp[j - weight[i]] + value[i] 表示 容量为 j - 物品 i 重量 的背包 加上 物品 i 的价值。（也就是容量为 j 的背包，放入物品 i 了之后的价值即：dp[j]）；

  - 递推公式为：dp[j] = max(dp[j], dp[j - weight[i]] + value[i])。

- dp 数组如何初始化：物品价值都是大于 0 的，所以 dp 数组初始化的时候，都初始为 0 就可以了。

- 确定遍历顺序

  ```javascript
  for (let i = 0; i < weight.size(); i++) {
    // 遍历物品
    for (let j = bagWeight; j >= weight[i]; j--) {
      // 遍历背包容量
      dp[j] = max(dp[j], dp[j - weight[i]] + value[i]);
    }
  }
  ```

  二维 dp 遍历的时候，背包容量是从小到大，而一维 dp 遍历的时候，背包是从大到小。

  **倒序遍历是为了保证物品 i 只被放入一次**。但如果一旦正序遍历了，那么物品 0 就会被重复加入多次。

  举一个例子：物品 0 的重量 weight[0] = 1，价值 value[0] = 15

  如果正序遍历

  dp[1] = dp[1 - weight[0]] + value[0] = 15

  dp[2] = dp[2 - weight[0]] + value[0] = 30

  此时 dp[2]就已经是 30 了，意味着物品 0，被放入了两次，所以不能正序遍历。

  为什么倒序遍历，就可以保证物品只放入一次呢？

  倒序就是先算 dp[2]

  dp[2] = dp[2 - weight[0]] + value[0] = 15 （dp 数组已经都初始化为 0）

  dp[1] = dp[1 - weight[0]] + value[0] = 15

  所以从后往前循环，每次取得状态不会和之前取得状态重合，这样每种物品就只取一次了。

**完整实现代码：**

```javascript
function testWeightBagProblem(wight, value, size) {
  const len = wight.length,
    dp = Array(size + 1).fill(0);
  for (let i = 1; i <= len; i++) {
    for (let j = size; j >= wight[i - 1]; j--) {
      dp[j] = Math.max(dp[j], value[i - 1] + dp[j - wight[i - 1]]);
    }
  }
  return dp[size];
}

function test() {
  console.log(testWeightBagProblem([1, 3, 4, 5], [15, 20, 30, 55], 6));
}

test();
```

### 01背包应用实例

**1、leetcode 416.分割等和子集**  https://leetcode.cn/problems/partition-equal-subset-sum/

![](/img/algorithm/DP/leetcode416.png)

**解题思路**

- 01背包中，dp[j] 表示： 容量为j的背包，所背的物品价值可以最大为dp[j]。**套到本题，dp[j]表示总容量为j的背包，最大可以凑成j的子集总和为dp[j]**；

- dp[j] = max(dp[j]，dp[j - nums[i]] + nums[i])；

- 非0下标的元素初始化为0

- 因为使用了一维数组，遍历顺序如下：

  ```javascript
    // 开始 01背包
    for(let i = 0; i < nums.size(); i++) {
        for(let j = target; j >= nums[i]; j--) { // 每一个元素一定是不可重复放入，所以从大到小遍历
            dp[j] = max(dp[j], dp[j - nums[i]] + nums[i]);
        }
    }
    ```

**代码实现**

```javascript
var canPartition = function(nums) {
    const sum = (nums.reduce((p, v) => p + v));
    if (sum & 1) return false;
    const dp = Array(sum / 2 + 1).fill(0);
    for(let i = 0; i < nums.length; i++) {
        for(let j = sum / 2; j >= nums[i]; j--) {
            dp[j] = Math.max(dp[j], dp[j - nums[i]] + nums[i]);
            if (dp[j] === sum / 2) {
                return true;
            }
        }
    }
    return dp[sum / 2] === sum / 2;
};
```

**2、leetcode 1049.最后一块石头的重量 II**  https://leetcode.cn/problems/last-stone-weight-ii/

![](/img/algorithm/DP/leetcode1049.png)

**解题思路**

- 01背包中，dp[j] 表示： 容量为j的背包，所背的物品价值可以最大为dp[j]。**套到本题，dp[j]表示总容量为j的背包，最大可以背dp[j]重量的石头**；

- dp[j] = max(dp[j], dp[j - stones[i]] + stones[i])；

- dp[j] = 0；

- 因为使用了一维数组，遍历顺序如下：

  ```javascript
    for (let i = 0; i < stones.size(); i++) { // 遍历物品
        for (let j = target; j >= stones[i]; j--) { // 遍历背包
            dp[j] = max(dp[j], dp[j - stones[i]] + stones[i]);
        }
    }
    ```

**代码实现**

```javascript
var lastStoneWeightII = function (stones) {
    let sum = stones.reduce((s, n) => s + n);

    let dpLen = Math.floor(sum / 2);
    let dp = new Array(dpLen + 1).fill(0);

    for (let i = 0; i < stones.length; ++i) {
        for (let j = dpLen; j >= stones[i]; --j) {
            dp[j] = Math.max(dp[j], dp[j - stones[i]] + stones[i]);
        }
    }

    return (sum - dp[dpLen]) - dp[dpLen];
};
```

## 完全背包理论基础

> 有N件物品和一个最多能背重量为W的背包。第i件物品的重量是weight[i]，得到的价值是value[i] 。**每件物品都有无限个（也就是可以放入背包多次）**，求解将哪些物品装入背包里物品价值总和最大。

**完全背包和01背包问题唯一不同的地方就是，每种物品有无限件：体现在遍历顺序上。**

完全背包的物品是可以添加多次的，所以要从小到大去遍历：

```javascript
// 先遍历物品，再遍历背包
for(let i = 0; i < weight.size(); i++) { // 遍历物品
    for(let j = weight[i]; j <= bagWeight ; j++) { // 遍历背包容量
        dp[j] = max(dp[j], dp[j - weight[i]] + value[i]);

    }
}
```

**完整实现代码：**

```javascript
// 先遍历物品，再遍历背包容量
function test_completePack1() {
    let weight = [1, 3, 5];
    let value = [15, 20, 30];
    let bagWeight = 4 ;
    let dp = new Array(bagWeight + 1).fill(0);
    for(let i = 0; i <= weight.length; i++) {
        for(let j = weight[i]; j <= bagWeight; j++) {
            dp[j] = Math.max(dp[j], dp[j - weight[i]] + value[i]);
        }
    }
    console.log(dp);
}

// 先遍历背包容量，再遍历物品
function test_completePack2() {
    let weight = [1, 3, 5];
    let value = [15, 20, 30];
    let bagWeight = 4 ;
    let dp = new Array(bagWeight + 1).fill(0);
    for(let j = 0; j <= bagWeight; j++) {
        for(let i = 0; i < weight.length; i++) {
            if (j >= weight[i]) {
                dp[j] = Math.max(dp[j], dp[j - weight[i]] + value[i]);
            }
        }
    }
    console.log(2, dp);
}
```

### 完全背包应用实例

**求装满背包有几种方法，一般公式都是：dp[j] += dp[j - nums[i]]；**

**遍历顺序：**

- **如果求组合数就是外层for循环遍历物品，内层for遍历背包**

- **如果求排列数就是外层for遍历背包，内层for循环遍历物品**

**1、leetcode 518.零钱兑换 II**  https://leetcode.cn/problems/coin-change-2/

![](/img/algorithm/DP/leetcode518.png)

**解题思路**

- 凑成总金额j的货币组合数为dp[j]；

- dp[j] （考虑coins[i]的组合总和） 就是所有的dp[j - coins[i]]（不考虑coins[i]）相加。所以递推公式：dp[j] += dp[j - coins[i]]；

- 下标非0的dp[j]初始化为0；

  ```javascript
  for (let i = 0; i < coins.size(); i++) { // 遍历物品
      for (let j = coins[i]; j <= amount; j++) { // 遍历背包容量
          dp[j] += dp[j - coins[i]];
      }
  }
  ```

**代码实现**

```javascript
var change = function (amount, coins) {
    let dp = Array(amount + 1).fill(0);
    dp[0] = 1;

    for (let i = 0; i < coins.length; i++) {
        for (let j = coins[i]; j <= amount; j++) {
            dp[j] += dp[j - coins[i]];
        }
    }

    return dp[amount];
};
```

**2、leetcode 377.组合总和 IV**  https://leetcode.cn/problems/combination-sum-iv/

![](/img/algorithm/DP/leetcode377.png)

**解题思路**

- dp[i]: 凑成目标正整数为i的排列个数为dp[i]，题目说元素相同顺序不同的组合算两个组合，所以这道题是求排列；
- 递推公式：dp[i] += dp[i - nums[j]]；
- 下标非0的dp[j]初始化为0；
- 遍历顺序为：背包 -> 物品。

**代码实现**

```javascript
var combinationSum4 = function (nums, target) {
    let dp = Array(target + 1).fill(0);
    dp[0] = 1;

    for (let i = 0; i <= target; i++) {
        for (let j = 0; j < nums.length; j++) {
            if (i >= nums[j]) {
                dp[i] += dp[i - nums[j]];
            }
        }
    }

    return dp[target];
};
```

**3、leetcode 70.爬楼梯（高级版）**  https://leetcode.cn/problems/climbing-stairs/

> 以前我没得选，现在我选择带上背包再爬一次！

![](/img/algorithm/DP/leetcode70.png)

**题目改为：一步一个台阶，两个台阶，三个台阶，.......，直到 m个台阶。问有多少种不同的方法可以爬到楼顶呢？**

1阶，2阶，.... m阶就是物品，楼顶就是背包。

**解题思路**

- dp[i]：爬到有i个台阶的楼顶，有dp[i]种方法，本题是求排列；
- 递推公式：dp[i] += dp[i - j]；
- 下标非0的dp[j]初始化为0；
- 遍历顺序为：背包 -> 物品。

**代码实现**

```javascript
var climbStairs = function(n) {
    const dp = new Array(n + 1).fill(0);
    const m = 2;
    dp[0] = 1;
    for(let i = 1; i <= n; i++){
        for(let j = 1; j <= m; j++){
            if(i >= j) {
	    	dp[i] += dp[i - j];
	       }
        }
    }
    return dp[n];
};
```

**4、leetcode 322.零钱兑换**  https://leetcode.cn/problems/coin-change/

![](/img/algorithm/DP/leetcode322.png)

**解题思路**

- dp[j]：凑足总额为j所需钱币的最少个数为dp[j]；
- 递推公式：dp[j] = min(dp[j - coins[i]] + 1, dp[j])；
- dp[0] = 0；
- 遍历顺序为：均可。

**代码实现**

```javascript
var coinChange = function (coins, amount) {
    if (!amount) {
        return 0;
    }

    let dp = Array(amount + 1).fill(Infinity);
    dp[0] = 0;

    for (let i = 0; i < coins.length; i++) {
        for (let j = coins[i]; j <= amount; j++) {
            dp[j] = Math.min(dp[j - coins[i]] + 1, dp[j]);
        }
    }

    return dp[amount] === Infinity ? -1 : dp[amount];
};
```

**5、leetcode 279.完全平方数**  https://leetcode.cn/problems/perfect-squares/

![](/img/algorithm/DP/leetcode279.png)

**解题思路**

- dp[j]：和为j的完全平方数的最少数量为dp[j]；
- 递推公式：dp[j] = min(dp[j - i * i] + 1, dp[j])；
- 下标非0的dp[j]初始化为最大值；
- 遍历顺序为：均可。

**代码实现**

```javascript
// 先遍历物品，再遍历背包
var numSquares1 = function (n) {
    let dp = new Array(n + 1).fill(Infinity)
    dp[0] = 0

    for (let i = 1; i ** 2 <= n; i++) {
        let val = i ** 2
        for (let j = val; j <= n; j++) {
            dp[j] = Math.min(dp[j], dp[j - val] + 1)
        }
    }
    return dp[n]
};
// 先遍历背包，再遍历物品
var numSquares2 = function (n) {
    let dp = new Array(n + 1).fill(Infinity)
    dp[0] = 0

    for (let i = 1; i <= n; i++) {
        for (let j = 1; j * j <= i; j++) {
            dp[i] = Math.min(dp[i - j * j] + 1, dp[i])
        }
    }

    return dp[n]
};
```

**6、leetcode 139.单词拆分**  https://leetcode.cn/problems/word-break/

> 单词就是物品，字符串s就是背包，单词能否组成字符串s，就是问物品能不能把背包装满。拆分时可以重复使用字典中的单词，说明就是一个完全背包。

![](/img/algorithm/DP/leetcode139.png)

**解题思路**

- dp[i] : 字符串长度为i的话，dp[i]为true，表示可以拆分为一个或多个在字典中出现的单词；
- 递推公式：如果确定dp[j] 是true，且 [j, i] 这个区间的子串出现在字典里，那么dp[i]一定是true，（j < i ）。if([j, i] 这个区间的子串出现在字典里 && dp[j]是true) 那么 dp[i] = true；
- 下标非0的dp[i]初始化为false，dp[0] = true；
- 遍历顺序为：均可。

**代码实现**

```javascript
var wordBreak = function (s, wordDict) {
    let dp = Array(s.length + 1).fill(false);
    dp[0] = true;

    for (let i = 0; i <= s.length; i++) {
        for (let j = 0; j < wordDict.length; j++) {
            if (i >= wordDict[j].length) {
                if (s.slice(i - wordDict[j].length, i) === wordDict[j] && dp[i - wordDict[j].length]) {
                    dp[i] = true
                }
            }
        }
    }

    return dp[s.length];
};
```

