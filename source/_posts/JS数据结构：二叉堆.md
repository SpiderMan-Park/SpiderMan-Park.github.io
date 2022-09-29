---
title: JS数据结构：二叉堆
date: 2022-09-22 23:33:43
tags:
  - 数据结构
categories:
  - 数据结构
keywords:
  - 数据结构
abbrlink: 二叉堆
top_img: /img/dataStructures/dataStructures.webp
cover: /img/dataStructures/dataStructures.webp
---

# 二叉堆

### 基本概念

- **二叉堆(堆)是特殊的二叉树。**

- **二叉堆是完全二叉树(即，每一层都有左侧和右侧子节点,最后一层叶子节点,尽可能都是左侧节点)。**

- **二叉堆分为最大堆和最小堆，最大堆可以快速导出树的最大值，最小堆可以快速导出树的最小值，所有的节点都大于等于（最大堆）或小于等于（最小堆）每个它的子节点。**

### 应用场景

- **二叉堆能够高效地找出最大值和最小值。**

- **常用于优先队列中，也就是堆排序中。**

### 二叉堆与二叉搜索树

- **二叉堆是二叉树.但不一定是二叉搜索树。**

- **二叉堆分为最大堆和最小堆。**

- **在最大堆中所有子节点都要小于等于父节点。**

- **在最小堆中所有子节点都要大于等于父节点。**

- **在二叉搜索树(BST)中，左侧子节点总是比父节点小，右侧子节点总是比父节点大。**

### 构造最小堆

**构建最小堆需要注意的是:**

- **插入元素是不可以选位置的，需要从底部做上移操作。**

- **删除元素我们也是不可以任意选位置的，我们删除的是堆顶元素，而且如果有子元素的话我们需要做下移操作。**

```javascript
class MinHeap {
  constructor() {
    this.heap = [];
  }

  // 根据父节点索引获取左侧子节点索引
  getLeftIndex(index) {
    return 2 * index + 1;
  }

  // 根据父节点索引获取右侧子节点索引
  getRightIndex(index) {
    return 2 * index + 2;
  }

  // 根据子节点索引获取父节点索引
  getParentIndex(index) {
    // 索引为0,则没有父节点
    if (index === 0) {
      return undefined;
    }
    // (index - 1)/2 然后向下取整
    return Math.floor((index - 1) / 2);
  }
  // 插入节点
  insert(value) {
    /**
     * 返回 true 表示插入成功
     * 返回 false 表示插入失败
     *
     * 插入成功和失败,取决于插入的数据类型是否为数字
     *
     * 插入的具体方式:
     * 1. 直接给heap.push一个元素
     * 2. 根据最后一个索引,执行上移操作
     */
    if (value != null) {
      this.heap.push(value);
      this.siftUp(this.heap.length - 1);
      return true;
    }
    return false;
  }
  // 上移
  siftUp(index) {
    /**
     * 1. 根据子节点索引获取父节点索引
     * 2. 执行上移操作的范围:上移操作的范围index 不能等于 0.
     * 3. 执行上移操作的条件: 父节点大于子节点.
     * 4. 执行上移操作的方法: 父节点和子节点调换位置.
     * 5. 执行上移操作的迭代: 子节点的索引不断替换成父节点的索引.
     */
    let parent = this.getParentIndex(index);
    while (index > 0 && this.heap[parent] > this.heap[index]) {
      [this.heap[parent], this.heap[index]] = [
        this.heap[index],
        this.heap[parent],
      ];
      index = parent;
      parent = this.getParentIndex(index);
    }
  }

  size() {
    return this.heap.length;
  }
  isEmpty() {
    return this.size() === 0;
  }

  // 获取堆顶
  findMinimum() {
    return this.isEmpty() ? undefined : this.heap[0];
  }
  // 移除堆顶
  extract() {
    // 如果是空的就不移除
    if (this.isEmpty()) {
      return undefined;
    }
    // 如果只有一个元素就只移除,不做下移操作
    if (this.size() === 1) {
      return this.heap.shift();
    }
    // 如果有多个元素,则需要做下移操作
    const removedValue = this.heap.shift();
    this.siftDown(0);
    return removedValue;
  }
  // 下移
  siftDown(index) {
    /**
     * 1. 根于父元素索引,获取左右两边的子元素索引
     * 2. 获取堆的长度.
     *
     * 3. 由于我们这里的递归比较复杂我们不用while,直接使用函数递归.
     * 4. 具体递归操作:
     * 4.1 保证left/right是非空的前提下,用左中右去替换父级索引,知道不需要替换为止.
     * 4.2 谁小谁要跟本尊替换
     */
    let element = index;
    const left = this.getLeftIndex(index); // {1}
    const right = this.getRightIndex(index); // {2}
    const size = this.size();
    if (left < size && this.heap[element] > this.heap[left]) {
      element = left;
    }
    if (right < size && this.heap[element] > this.heap[right]) {
      element = right;
    }
    // 如果这把不需要替换,就说明没必要在在替换下去
    if (index !== element) {
      [this.heap[index], this.heap[element]] = [
        this.heap[element],
        this.heap[index],
      ]; // 需要替换就替换 (下移)
      this.siftDown(element); // 替换之后还需要递归向下检查
    }
  }
}

const minHeap = new MinHeap();

minHeap.insert(3);
minHeap.insert(5);
minHeap.insert(1);
minHeap.insert(6);
minHeap.extract();
console.log(minHeap.findMinimum());
minHeap.extract();
console.log(minHeap.findMinimum());
```