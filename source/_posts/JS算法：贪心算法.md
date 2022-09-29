---
title: JS算法：贪心算法
date: 2022-09-29 15:34:24
tags:
  - 算法
categories:
  - 算法
keywords:
  - 算法
top_img: /img/algorithm/Greedy/Greedy.jpg
cover: /img/algorithm/Greedy/Greedy.jpg
---

# 贪心算法

## 贪心算法理论基础

> 贪心法，又称贪心算法，贪婪算法，在对问题求解时，总是做出在当前看来最好的选择，期望通过每个阶段的局部最优选择达到全局最优，但结果不一定最优。

**题目特点：**

如何能够确定一道题是否可以用贪心算法求解呢？

因为贪心算法求得的结果不一定是整体最优解，所以很难断定它就一定可以用贪心算法。

可以用贪心算法的题目一般具有以下特性：

- **贪心选择性质**
  简单的说，问题能够分解成子问题来解决，子问题的最优解能递推到最终问题的最优解，就能用贪心算法的到最后的最优解。

  贪心算法与动态规划的不同点在于它对每个子问题的解决方案都做出当前的最优选择，不能回退，而动态规划会保留之前的运算结果，并根据之前的结果进行选择，有回退的功能，贪心是动态规划的理想化的情况。

- **最优子结构性质**

  当一个问题的最优解包含其子问题的最优解时，称此问题具有最优子结构性质。

  问题的最优子结构性质是该问题可用动态规划算法或贪心算法求解的关键特征。

**解题思路：**

一般可以使用贪心算法的题，也可以使用动态规划去做，但是考虑到动态规划的特点是求整体的最优解，在大部分题中贪心要比动态规划时空复杂度更低，也更容易实现。

- 把求解的问题分解成若干个子问题；
- 对每一子问题求解，得到子问题的局部最优解；
- 把子问题的解局部最优解，合成原来解问题的一个解。

## 贪心算法实例

**1、leetcode 455.分发饼干**  https://leetcode.cn/problems/assign-cookies/

![](/img/algorithm/Greedy/leetcode455.png)

**解题思路**

- 大尺寸的饼干可以同时满足大胃口和小胃口的孩子；
- 饼干数组和孩子数组分别从小到大进行排序，并从右向左遍历，要先满足大胃口的孩子。

**代码实现**

```javascript
var findContentChildren = function (g, s) {
    g = g.sort((a, b) => { return a - b });
    s = s.sort((a, b) => { return a - b });
    let res = 0;
    let index = s.length - 1;
    for (let i = g.length - 1; i >= 0; i--) {
        // 从大胃口的小孩开始满足
        if (index >= 0 && s[index] >= g[i]) {
            res++;
            index--;
        }
    }
    return res
};
```

**2、leetcode 1005.k次取反后最大化的数组和**  https://leetcode.cn/problems/maximize-sum-of-array-after-k-negations/

![](/img/algorithm/Greedy/leetcode1005.png)

**解题思路**

- 将数组按照绝对值大小从大到小排序，**注意要按照绝对值的大小**；
- 从前向后遍历，遇到负数将其变为正数，同时k--；
- 如果K还大于0，那么反复转变数值最小的元素，将K用完；
- 对数组求和。

**代码实现**

```javascript
var largestSumAfterKNegations = function (nums, k) {
    nums.sort((a, b) => Math.abs(b) - Math.abs(a)); // 排序
    let sum = 0;
    for (let i = 0; i < nums.length; i++) {
        if (nums[i] < 0 && k-- > 0) { // 负数取反（k 数量足够时）
            nums[i] = -nums[i];
        }
        sum += nums[i]; // 求和
    }
    if (k % 2 > 0) { // k 有多余的（k若消耗完则应为 -1）
        sum -= 2 * nums[nums.length - 1]; // 减去两倍的最小值（因为之前加过一次）
    }
    return sum;
};
```

**3、leetcode 860.柠檬水找零**  https://leetcode.cn/problems/lemonade-change/

![](/img/algorithm/Greedy/leetcode860.png)

**解题思路**

- 优先找面额大的。

**代码实现**

```javascript
var lemonadeChange = function (bills) {
    let five = 0, ten = 0;
    for (const money of bills) {
        if (money === 5) { // 面值为5 直接可以兑换柠檬水
            five += 1;
        } else if (money === 10) { // 面值为10 兑换柠檬水 还需要找5元
            if (five === 0) {
                return false;
            }
            five -= 1;
            ten += 1;
        } else { // 面值为20 兑换柠檬水 需要找3个5元或一个10元一个5元
            if (five > 0 && ten > 0) {
                five -= 1;
                ten -= 1;
            } else if (five >= 3) {
                five -= 3;
            } else {
                return false;
            }
        }
    }
    return true;
};
```

**4、leetcode 452.用最少数量的箭引爆气球**  https://leetcode.cn/problems/minimum-number-of-arrows-to-burst-balloons/

![](/img/algorithm/Greedy/leetcode452.png)

![](/img/algorithm/Greedy/leetcode452_animation.png)

**解题思路**

- 区间按照结尾从小到大排序，循环数组，如果后面一个区间的开始大于前一个区间的结尾 就需要新增一支箭。

**代码实现**

```javascript
var findMinArrowShots = function (points) {
    if (!points.length) {
        return 0;
    }

    points.sort((a, b) => a[1] - b[1]); // 按照区间大小排序
    let position = points[0][1];
    let res = 1; // 至少需要一支箭
    for (let balloon of points) {
        if (balloon[0] > position) {
            // 如果后面一个区间的开始大于前一个区间的结尾 就需要新增一支箭
            position = balloon[1]; // 更新position为新的区间的结尾
            res++;
        }
    }
    return res;
};
```

**5、leetcode 134.加油站**  https://leetcode.cn/problems/gas-station/

![](/img/algorithm/Greedy/leetcode134.png)

**解题思路**

- 首先判断总油量是否小于总油耗，如果是则肯定不能走一圈。如果否，那肯定能跑一圈。接下来就是循环数组，从第一个站开始，计算每一站剩余的油量，如果油量为负了，就以这个站为起点从新计算。如果到达某一个点为负，说明起点到这个点中间的所有站点都不能到达该点。

**代码实现**

```javascript
var canCompleteCircuit = function (gas, cost) {
    let totalGas = 0; // 总油量
    let totalCost = 0; // 总油耗
    for (let i = 0; i < gas.length; i++) {
        totalGas += gas[i];
        totalCost += cost[i];
    }
    if (totalGas < totalCost) { // 总油量小于总油耗 不能走一圈
        return -1;
    }

    let currentGas = 0;
    let start = 0;
    for (let i = 0; i < gas.length; i++) {
        currentGas = currentGas - cost[i] + gas[i];
        if (currentGas < 0) { // 如果到达下一站的时候油量为负数 就以这个站为起点 重新计算
            currentGas = 0;
            start = i + 1;
        }
    }

    return start;
};
```

**6、leetcode 435.无重叠区间**  https://leetcode.cn/problems/non-overlapping-intervals/

![](/img/algorithm/Greedy/leetcode435.png)

**解题思路**

- intervals按右边界排序，然后从左往右遍历，右边界结束的越早，留给后面的区间的空间就越大，不重合的区间个数就越多，intervals的长度减去最多的不重复的区间 就是最少删除区间的个数。

**代码实现**

```javascript
var eraseOverlapIntervals = function (intervals) {
    if (!intervals.length) {
        return 0;
    }
    intervals.sort((a, b) => a[1] - b[1]);

    const len = intervals.length;
    let right = intervals[0][1]; // right初始化为第一个区间的右边界
    let res = 1; // 最多的不重合区间的个数
    for (let i = 1; i < len; i++) {
        // 循环区间数组
        if (intervals[i][0] >= right) {
            // 当区间的左边界大于上一个区间的右边界的时候 说明是一对不重合区间
            res++; // res加1
            right = intervals[i][1]; // 更新right
        }
    }
    return len - res; // intervals的长度减去最多的不重复的区间 就是最少删除区间的个数
};
```

**7、leetcode 55.跳跃游戏**  https://leetcode.cn/problems/jump-game/

![](/img/algorithm/Greedy/leetcode55.png)

![](/img/algorithm/Greedy/leetcode55_animation.jpg)

**解题思路**

- 不用考虑每一步跳跃到那个位置，而是尽可能的跳跃到最远的位置，看最多能覆盖的位置，不断更新能覆盖的距离。

**代码实现**

```javascript
var canJump = function (nums) {
    if (nums.length === 1) return true; // 长度为1 直接就是终点
    let cover = nums[0]; // 能覆盖的最远距离
    for (let i = 0; i <= cover; i++) {
        cover = Math.max(cover, i + nums[i]); // 当前覆盖距离cover和当前位置加能跳跃的距离中取一个较大者
        if (cover >= nums.length - 1) {
            // 覆盖距离超过或等于nums.length - 1 说明能到达终点
            return true;
        }
    }
    return false; // 循环完成之后 还没返回true 就是不能达到终点
};
```

