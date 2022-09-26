---
title: JS算法：分而治之
date: 2022-09-24 18:16:08
tags:
  - 算法
categories:
  - 算法
keywords:
  - 算法
top_img: /img/algorithm/DivideConquer/DivideConquer.jpg
cover: /img/algorithm/DivideConquer/DivideConquer.jpg
---

# 分治算法（Divide & Conquer）

## 分治算法思想

- 分治算法的核心思想就是，分而治之，将原问题划分成n个规模较小，并且结构与原问题相似的子问题，递归地解决这些子问题，然后再合并其结果，就得到原问题的解。

- 分治算法一般都比较适合用递归来实现。分治算法的递归实现中，每一层递归都会涉及这样三个操作：
  - **分解**：将原问题划分为规模较小的子问题，子问题相互独立，与原问题形式相同；
  - **解决**：递归地求解各个子问题，若子问题足够小，则直接求解；
  - **合并**：这一步非必须。有些问题涉及合并子问题的解，将子问题的解合并成原问题的解。有的问题则不需要，只是求出子问题的解即可。

## **分治算法适用情况**

- 规模大
- 可分解
- 各独立
- 可合并

**规模大和可分解针对原始问题：**

- 即原问题要规模比较大，不易直接解决，但是问题分解成规模较小的相同子问题比较容易解决。

**各独立和可合并针对子问题：**

- 即子问题之间求解是相关独立互不影响，且子问题的解可以合并成原始问题的解。

## **分治算法实例**

**1、二分查找**

**2、快速排序**

**3、归并排序**

**4、leetcode 169.多数元素**  https://leetcode.cn/problems/majority-element/

![](/img/algorithm/DivideConquer/leetcode169.png)

  **分治法解题思路：**

  - 确定切分的终止条件，直到所有的子问题都是长度为 1 的数组，停止切分。

  - 准备数据，将大问题切分为小问题，递归地将原数组二分为左区间与右区间，直到最终的数组只剩下一个元素，将其返回

  - 处理子问题得到子结果，并合并
    - 长度为 1 的子数组中唯一的数显然是众数，直接返回即可。
    - 如果它们的众数相同，那么显然这一段区间的众数是它们相同的值。
    - 如果他们的众数不同，比较两个众数在整个区间内出现的次数来决定该区间的众数

  **代码实现：**

  ```javascript
  var majorityElement = function (nums) {
    const getMode = (low, high) => {
      if (low === high) return nums[low];
  
      //拆分成更小的区间，一分为二
      let mid = Math.floor((low + high) / 2);
  
      let left = getMode(low, mid);
      let right = getMode(mid + 1, high);
  
      if (left === right) return left;
  
      let leftCount = getCount(left, low, high); // 统计区间内 left 的个数
      let rightCount = getCount(right, low, high); // 统计区间内 right 的个数
  
      return leftCount > rightCount ? left : right; // 返回 left 和 right 中个数多的那个
    };
  
    //统计 low 到 high 之间 num 的数量
    var getCount = (num, low, high) => {
      let count = 0;
      for (let i = low; i <= high; i++) {
        if (nums[i] === num) count++;
      }
      return count;
    };
  
    return getMode(0, nums.length - 1);
  };
  ```

**5、leetcode 53.最大子数组合**  https://leetcode.cn/problems/maximum-subarray/

![](/img/algorithm/DivideConquer/leetcode53.png)

  **分治法解题思路：**

  - 确定切分的终止条件，直到所有的子问题都是长度为 1 的数组，停止切分。

  - 准备数据，将大问题切分为小问题，递归地将原数组二分为左区间与右区间，直到最终的数组只剩下一个元素，将其返回。
  - 处理子问题得到子结果，并合并
    - 将数组切分为左右区间：
      - 对与左区间：从右到左计算左边的最大子序和。
      - 对与右区间：从左到右计算右边的最大子序和。
    - 由于左右区间计算累加和的方向不一致，因此，左右区间直接合并相加之后就是整个区间的和。
    - 最终返回左区间的元素、右区间的元素、以及整个区间(相对子问题)和的最大值。

  **代码实现：**

  ```javascript
  function crossSum(nums, left, right, mid) {
      if (left === right) {//左右相等 返回左边的值
          return nums[left];
      }
  
      let leftMaxSum = Number.MIN_SAFE_INTEGER;//左边最大值初始化
      let leftSum = 0;
      for (let i = mid; i >= left; --i) {
          leftSum += nums[i];
          leftMaxSum = Math.max(leftMaxSum, leftSum);//更新左边最大子序和
      }
  
      let rightMaxSum = Number.MIN_SAFE_INTEGER;
      let rightSum = 0;
      for (let i = mid + 1; i <= right; ++i) {
          rightSum += nums[i];
          rightMaxSum = Math.max(rightMaxSum, rightSum);//更新右边最大子序和
      }
  
      return leftMaxSum + rightMaxSum;//返回左右合并之后的最大子序和
  }
  
  function _maxSubArray(nums, left, right) {
      if (left === right) {//递归终止条件
          return nums[left];
      }
  
      const mid = Math.floor((left + right) / 2);
      const lsum = _maxSubArray(nums, left, mid);//左边最大子序和
      const rsum = _maxSubArray(nums, mid + 1, right);//右边最大子序和
      const cross = crossSum(nums, left, right, mid);//合并左右的之后的最大子序和
  
      return Math.max(lsum, rsum, cross);//返回3中子序和中最大的
  }
  
  var maxSubArray = function(nums) {
      return _maxSubArray(nums, 0, nums.length - 1);
  };
  ```

**6、leetcode 374.猜数字大小**  https://leetcode.cn/problems/guess-number-higher-or-lower/

![](/img/algorithm/DivideConquer/leetcode374.png)

  **代码实现：**

  ```javascript
  var guessNumber = function (n) {
    // 递归函数 接受一个搜索范围
    const rec = (low, high) => {
      // 递归结束条件
      if (low > high) return;
  
      // 获取中间元素
      let mid = Math.round(low + (high - low) / 2);
  
      // 判断是否猜对
      const res = guess(mid);
  
      // 猜对
      if (res === 0) {
        return mid;
      } else if (res === 1) {
        // 猜大了
        return rec(mid + 1, high);
      } else {
        // 猜小了
        return rec(low, mid - 1);
      }
    };
  
    return rec(1, n);
  };
  ```

**7、leetcode 226.翻转二叉树**  https://leetcode.cn/problems/invert-binary-tree/

![](/img/algorithm/DivideConquer/leetcode226.png)

  **代码实现：**

  ```javascript
  var invertTree = function (root) {
    if (root === null) return null;
  
    let left = invertTree(root.left);
    let right = invertTree(root.right);
  
    root.left = right;
    root.right = left;
  
    return root;
  };
  ```

**8、leetcode 101.对称二叉树**  https://leetcode.cn/problems/symmetric-tree/

![](/img/algorithm/DivideConquer/leetcode101.png)

  **分治法解题思路：**

  - 分:获取两个树的左子树和右子树。
  - 解:递归地判断树1的左子树和树2的右子树是否镜像，树1的右子树和树2的左子树是否镜像。
  - 合:如果上述都成立，且根节点值也相同，两个树就镜像。

  **代码实现：**

  ```javascript
  var isSymmetric = function (root) {
    if (!root) return true;
    const isMirror = (left, right) => {
      if (!left && !right) return true; // 两个空子树为镜像
      if (
        left &&
        right &&
        left.val === right.val &&
        isMirror(left.left, right.right) &&
        isMirror(left.right, right.left)
      ) {
        return true;
      }
      return false;
    };
    return isMirror(root.left, root.right);
  };
  ```

**9、leetcode 100.相同的树**  https://leetcode.cn/problems/same-tree/

![](/img/algorithm/DivideConquer/leetcode100.png)

  **分治法解题思路：**

  - 分:获取两个树的左子树和右子树。
  - 解:递归地判断两个树的左子树是否相同，右子树是否相同。
  - 合:将上述结果合并，如果根节点的值也相同，树就相同。

  **代码实现：**

  ```javascript
  var isSameTree = function (p, q) {
      if (!p && !q) return true
      
      if (
        p && q
        && p.val === q.val
        && isSameTree(p.left, q.left)
        && isSameTree(p.right, q.right)
      ) return true
     
      return false
  };
  ```
