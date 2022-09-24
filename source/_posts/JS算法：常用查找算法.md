---
title: JS算法：常用查找算法
date: 2022-09-24 17:09:39
tags:
  - 算法
categories:
  - 算法
keywords:
  - 算法
top_img: /img/algorithm/search.jpeg
cover: /img/algorithm/search.jpeg
---

# JavaScript常用查找算法

## 顺序(线性)查找

> 它接受一个数组和一个值 - 并返回该值所在的索引。如果该值不存在，该函数将返回 -1

**步骤**

- 从 i 开始并遍历数组长度

- 检查当前数组元素是否等于给定值。

- 如果找到，则返回该值的索引

- 如果值不存在，则返回 -1 

**特性**

**优点：文件在查找前不需要进行任何处理和排序**

**缺点：查找速度慢，适用于小数据文件的查找**

```javascript
function linearSearch(arr, val){
    for(let i = 0; i < arr.length; i++){
        if(arr[i] === val) return i;
    }
    return -1;
}
```

平均和最差时间复杂度为 O(N)，最佳情况时间复杂度为 O(1)

## 二分查找

> 二分查找，也称折半查找，是一种在有序数组中查找特定元素的搜索算法，时间复杂度为 O(logn)。

**步骤**

- 选择数组的中间值；

- 如果待搜索值比选中值要小，则返回步骤 1 并在选中值左边的子数组中寻找（较小）；

-  如果待搜索值比选中值要大，则返回步骤 1 并在选种值右边的子数组中寻找（较大）；
- 如果选中值是待搜索值，那么算法执行完毕（值找到了）。

![](/img/algorithm/BinarySearch.gif)


**实现**

- **非递归**

```javascript
function binarySearch(arr, num) {
  if (num < arr[0] || num > arr[arr.length - 1]) {
    return -1;
  }
  var left = 0;
  var right = arr.length - 1;
  while (left <= right) {
    var mid = Math.floor((left + right) / 2);
    if (num > arr[mid]) {
      left = mid + 1;
    } else if (num < arr[mid]) {
      right = mid - 1;
    } else {
      return mid;
    }
  }
  return -1;
}
```

- **递归**

```javascript
function binarySearch2(arr, num, left, right) {
  var mid = Math.floor((left + right) / 2);
  if (left > right) {
    return -1;
  }
  if (arr[mid] === num) {
    return mid;
  } else if (num > arr[mid]) {
    return binarySearch2(arr, num, mid + 1, right);
  } else {
    return binarySearch2(arr, num, left, mid - 1);
  }
}
```

## 插值查找

> 根据查找关键字与查找表中最大最小记录关键字比较后的查找方法。插值查找基于二分查找，将查找点的选择改进为自适应选择，提高查找效率。
>
> 对于表长较大，而关键字分布又比较均匀的查找表来说，插值查找算法的平均性能比二分查找要好的多。
>
>时间复杂度为 O(logn)。

**实现**

- 非递归

```javascript
const insertSearch = (arr, value) => {
  let start = 0;
  let end = arr.length - 1;
  while (start <= end) {
    let mid =
      start + ((value - arr[start]) / (arr[end] - arr[start])) * (end - start);
    if (arr[mid] == value) {
      return mid;
    } else if (arr[mid] > value) {
      end = mid - 1;
    } else {
      start = mid + 1;
    }
  }
  return -1;
};
```

- 递归

```javascript
const insertSearchByRe = (arr, value, start, end) => {
  if (start > end) {
    return -1;
  }
  let mid =
    start + ((value - arr[start]) / (arr[end] - arr[start])) * (end - start);
  if (arr[mid] == value) {
    return mid;
  } else if (arr[mid] > value) {
    end = mid - 1;
    return insertSearchByRe(arr, value, start, end);
  } else {
    start = mid + 1;
    return insertSearchByRe(arr, value, start, end);
  }
};
```
