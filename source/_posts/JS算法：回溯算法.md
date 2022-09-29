---
title: JS算法：回溯算法
date: 2022-09-27 23:12:24
tags:
  - 算法
categories:
  - 算法
keywords:
  - 算法
abbrlink: 回溯
top_img: /img/algorithm/BackTracking/BackTracking.jpg
cover: /img/algorithm/BackTracking/BackTracking.jpg
---

# 回溯算法

## 回溯算法理论基础

> 回溯法也可以叫做回溯搜索法，它是一种搜索的方式。
>
> 回溯是递归的副产品，只要有递归就会有回溯。
>
> 回溯法其实就是暴力查找，不是高效的算法，回溯的本质是穷举，穷举所有可能，然后选出我想要的答案，如果想让回溯法高效一些，可以加一些剪枝的操作，但也改不了回溯法就是穷举的本质。
>
> 回溯和递归是相辅相成的。

**回溯法一般可以解决以下问题：**

- 组合问题：N个数里面按一定规则找出k个数的集合；
- 子集问题：一个N个数的集合里有多少符合条件的子集；
- 排列问题：N个数按一定规则全排列，有几种排列方式；
- 棋盘问题：N皇后，解数独等等。

**回溯：一层层递归，尝试搜素答案：**

- 找到答案：返回结果，尝试其他的分支；
- 找不到答案：返回上一层，尝试其他分支。

## 回溯算法步骤（模板）

- 回溯函数模板返回值以及参数
- 终止条件
- 单层递归逻辑
- 选择其他分支（撤销选择 重置状态）

```javascript
result = [];
function backtracking(path, list) {
    if (终止条件) {
        存放结果;
        result.push(path);
        return;
    }

    for (选择：本层集合中元素（树中节点孩子的数量就是集合的大小）) {
        // 单层逻辑
        处理节点;
        backtracking(路径，选择列表); // 递归
        回溯，撤销选择 重置状态
    }
}
```

## 组合问题

**1、leetcode 77.组合**  https://leetcode.cn/problems/combinations/

![](/img/algorithm/BackTracking/leetcode77.png)

**解题思路**

- 定义两个全局变量，一个用来存放符合条件单一结果path，一个用来存放符合条件结果的集合result；
- startIndex，这个参数用来记录本层递归的中，集合从哪里开始遍历；
- path这个数组的大小如果达到k，说明找到了一个子集大小为k的组合了;
- for循环每次从startIndex开始遍历，然后用path保存取到的节点i;
- for循环用来横向遍历，递归的过程是纵向遍历。

**可以剪枝的地方就在递归中每一层的for循环所选择的起始位置**。

**如果for循环选择的起始位置之后的元素个数 已经不足 我们需要的元素个数了，那么就没有必要搜索了**。

优化过程如下：

- 已经选择的元素个数：path.size()；

- 还需要的元素个数为: k - path.size()；

- 在集合n中至多要从该起始位置 : n - (k - path.size()) + 1，开始遍历。

为什么有个+1呢，因为包括起始位置，要的是一个左闭的集合。

**代码实现**

```javascript
let result = []
let path = []
var combine = function (n, k) {
    result = [];
    backtracking(n, k, 1);
    return result
};
const backtracking = (n, k, startIndex) => {
    if (path.length === k) {
        result.push([...path]);
        return
    }
    for (let i = startIndex; i <= n - (k - path.length) + 1; ++i) {
        path.push(i);
        backtracking(n, k, i + 1);
        path.pop();
    }
}
```

**2、leetcode 17.电话号码的字母组合**  https://leetcode.cn/problems/letter-combinations-of-a-phone-number/

![](/img/algorithm/BackTracking/leetcode17.png)

**解题思路**

- 数字和字母如何映射？使用map或者定义一个二维数组；
- 两个字母就两个for循环，三个字母就三个for循环？回溯；

**代码实现**

```javascript
var letterCombinations = function (digits) {
    const k = digits.length;
    const map = ["", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"];
    if (!k) return [];
    if (k === 1) return map[digits].split("");

    const res = [], path = [];
    backtracking(digits, k, 0);
    return res;

    function backtracking(digits, k, a) {
        if (path.length === k) {
            res.push(path.join(""));
            return;
        }
        for (const v of map[digits[a]]) {
            path.push(v);
            backtracking(digits, k, a + 1);
            path.pop();
        }

    }
};
```

**3、leetcode 39.组合总和**  https://leetcode.cn/problems/combination-sum/

![](/img/algorithm/BackTracking/leetcode39.png)

**解题思路**

- 先排序；
- 遍历整数数组，元素相加的和为sum，sum === target是，输出这些元素；
- 递归寻找所有解。

**代码实现**

```javascript
var combinationSum = function (candidates, target) {
    const res = [], path = [];
    candidates.sort((a, b) => a - b); // 排序
    backtracking(0, 0);
    return res;
    function backtracking(j, sum) {
        if (sum === target) {
            res.push(Array.from(path));
            return;
        }
        for (let i = j; i < candidates.length; i++) {
            const n = candidates[i];
            if (n > target - sum) break;
            // 标记
            path.push(n);
            sum += n;
            backtracking(i, sum);
            // 撤销标记
            path.pop();
            sum -= n;
        }
    }
};
```

**4、leetcode 40.组合总和 II**  https://leetcode.cn/problems/combination-sum-ii/

![](/img/algorithm/BackTracking/leetcode40.png)

**解题思路**

- 先要对candidates进行从小到大排序；

- 本题和39题唯一不同的是：
  - 39题原数组的每个元素可以重复使用；
  - 此题只能使用一次。

- 在递归后回溯，将sum和path恢复到原来的状态，传入的startIndex要加1，因为这里每个数字只能用一次。

**代码实现**

```javascript
var combinationSum2 = function (candidates, target) {
    let len = candidates.length;
    let res = [];
    let path = [];
    candidates = candidates.sort((a, b) => { return a - b })
    backtracking(path, target, 0);
    function backtracking(path, target, startIndex) {
        if (target == 0) {
            res.push(path);
            return;
        }
        for (let i = startIndex; i < len; i++) {
            if (target < candidates[i]) break;
            if (i > startIndex && candidates[i - 1] == candidates[i]) continue;
            path.push(candidates[i]);
            backtracking(path.slice(), target - candidates[i], i + 1);
            path.pop();
        }
    }
    return res;
};
```

## 子集问题

**1、leetcode 78.子集**  https://leetcode.cn/problems/subsets/

![](/img/algorithm/BackTracking/leetcode78.png)

**解题思路**

- 所有可能的子集， [ ] 也是子集；
- 没有说可以重复利用原数组的单个元素去组合子集，下次要从i+1开始。

**代码实现**

```javascript
var subsets = function (nums) {
    let res = []; // 存放结果
    let path = []; // 存放一个分支的结果
    function backtracking(startIndex) { // startIndex字符递归开始的位置
        res.push(path.slice()); // path.slice()断开和path的引用关系
        for (let i = startIndex; i < nums.length; i++) { // 从startIndex开始递归
            path.push(nums[i]); // 当前字符推入path
            backtracking(i + 1); // startIndex向后移动一个位置 继续递归
            path.pop(); // 回溯状态
        }
    }
    backtracking(0);
    return res;
};
```

**2、leetcode 90.子集 II**  https://leetcode.cn/problems/subsets-ii/

![](/img/algorithm/BackTracking/leetcode90.png)

**解题思路**

- 重点就在于如何处理重复的元素？
- 先排序；
- 在循环开始时，判断i是否大于startIndex以及当前元素是否和前一个元素相同。若相同，则跳过此次循环；
- 因为只有在i大于startIndex时，才表明前一个元素已被使用过，而使用过的元素不可以在同一个循环中再次被使用，因此设定这个判断条件，然后进入递归，返回时回溯。

**代码实现**

```javascript
var subsetsWithDup = function (nums) {
    let res = [];
    let path = [];
    nums = nums.sort((a, b) => { return a - b });

    function backtracking(startIndex) {
        res.push(Array.from(path));
        if (startIndex > nums.length - 1) {
            return;
        }
        for (let i = startIndex; i < nums.length; i++) {
            if (i > startIndex && nums[i] === nums[i - 1]) {
                continue;
            }
            path.push(nums[i]);
            backtracking(i + 1);
            path.pop();
        }
    }
    backtracking(0);
    return res;
};
```

## 排列问题

**1、leetcode 46.全排列**  https://leetcode.cn/problems/permutations/

![](/img/algorithm/BackTracking/leetcode46.png)

**解题思路**

- 准备path数组，存放每一个回溯递归的分支中的数字排列，调用回溯函数 传入nums，nums长度，used数组，used表示已经使用的数字，回溯函数中循环nums中的数，每层循环将nums中的元素加入path中，然后递归调用回溯函数，调用完成之后，回溯之前的状态，当path数组的长度和nums的长度相同就找到了一种排列。

**代码实现**

```javascript
// 方法一
var permute = function (nums) {
    let res = [];
    let path = [];
    function backtracking(path) {
        if (path.length === nums.length) {
            res.push(path);
            return;
        }
        for (let i = 0; i < nums.length; i++) {
            if (!path.includes(nums[i])) {
                path.push(nums[i]);
                backtracking(path.slice());
                path.pop();
            }
        }
    }
    backtracking(path);
    return res
};

// 方法二
var permute = function (nums) {
    let path = [];
    let res = [];
    backtracking(nums, nums.length, []); // 调用回溯函数 传入nums，nums长度，used数组
    return res;

    function backtracking(n, len, used) {
        if (path.length === len) { // 递归终止条件
            res.push(Array.from(path));
            return;
        }
        for (let i = 0; i < len; i++) {
            if (used[i]) continue; // 已经使用过了就跳过本轮循环
            path.push(n[i]);
            used[i] = true;
            backtracking(n, len, used); //递归
            path.pop(); // 回溯 将push进的元素pop出来 然后标记成未使用 继续其他分支
            used[i] = false;
        }
    }
};
```

**2、leetcode 47.全排列 II**  https://leetcode.cn/problems/permutations-ii/

![](/img/algorithm/BackTracking/leetcode47.png)

**解题思路**

- 要在同一层去重，去重条件为当当前元素与前一个元素相同，且前一个元素未在used里标记为true。即前一个元素在同一层被使用过了，并未在上一层被使用

**代码实现**

```javascript
var permuteUnique = function (nums) {
    let path = [];
    let res = [];
    nums = nums.sort((a, b) => { return a - b })
    backtracking(nums, nums.length, []); // 调用回溯函数 传入nums，nums长度，used数组
    return res;

    function backtracking(n, len, used) {
        if (path.length === len) { // 递归终止条件
            res.push(Array.from(path));
            return;
        }
        for (let i = 0; i < len; i++) {
            if (i > 0 && nums[i] === nums[i - 1] && !used[i - 1]) continue; // 去重
            if (used[i]) continue; // 已经使用过了就跳过本轮循环
            path.push(n[i]);
            used[i] = true;
            backtracking(n, len, used); //递归
            path.pop(); // 回溯 将push进的元素pop出来 然后标记成未使用 继续其他分支
            used[i] = false;
        }
    }
};
```

## 棋盘问题

**1、leetcode 51.N 皇后**  https://leetcode.cn/problems/n-queens/

![](/img/algorithm/BackTracking/leetcode51.png)

**解题思路**

- 皇后的约束条件：
  - 不能同行
  - 不能同列
  - 不能同斜线

- 本题是全排列的变种，只有细微的不同；
- 相同：这个是有nn的棋盘来放皇后，全排列也可以是一个nn的棋盘，也一样是每行都必须选一个数字；
- 不同：它们选择元素的限制条件不同，其实本质上就是添加了两个条件（斜方向上不能有棋子），其他都是相同的；
- 要封装一个isValid函数（判断棋盘合法性）；
- 以及最后要求返回的是元素是字符串的二维数组，也就是三维数组

**代码实现**

```javascript
var solveNQueens = function (n) {
    let res = [];
    let chessBoard = new Array(n).fill(0);
    function backtracking(row) {
        if (row === n) {
            res.push(chessBoard.concat());
            return;
        }
        chessBoard[row] = new Array(n).fill(".");
        for (let i = 0; i < n; i++) {
            if (isValid(chessBoard, row, i)) {
                chessBoard[row][i] = "Q";
                chessBoard[row] = chessBoard[row].toString().replaceAll(",", "");
                row++;
                backtracking(row);
                row--;
                chessBoard[row] = new Array(n).fill(".");
            }
        }

        function isValid(chessBoard, row, i) {
            if (row === 0) {
                return true;
            }

            for (let j = 0; j < row; j++) {
                if (chessBoard[j][i] === "Q") {
                    return false;
                }
            }

            for (let a = row - 1, b = i - 1; a > -1 && b > -1; a--, b--) {
                if (chessBoard[a][b] === "Q") {
                    return false
                }
            }

            for (let a = row - 1, b = i + 1; a > -1 && b < n; a--, b++) {
                if (chessBoard[a][b] === "Q") {
                    return false
                }
            }
            return true;
        }
    }
    backtracking(0);
    return res;
};
```