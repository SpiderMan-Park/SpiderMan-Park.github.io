---
title: JS数据结构：集合
date: 2022-09-21 18:39:59
tags:
  - 数据结构
categories:
  - 数据结构
keywords:
  - 数据结构
abbrlink: 集合
top_img: /img/dataStructures/dataStructures.webp
cover: /img/dataStructures/dataStructures.webp
---

# 集合

> 集合是由一组无序且唯一（即不能重复）的项组成的。该数据结构使用了与有限集合相同的数学概念，但应用在计算机科学的数据结构中。

## 一、创建集合类

```javascript
class Set {
    constructor() {
        this.items = {};
    }
    // 检验某个元素是否存在于集合中
    has(element) {
        return this.items.hasOwnProperty(element)
    }
    add(element) {
        // 如果集合中包含该元素
        if (this.has(element)) return false
        // 向集合中添加元素
        this.items[element] = element;
        return true
    }
    delete(element) {
        // 如果集合中没有包含该元素
        if (!this.has(element)) return false
        // 从集合中删除该元素
        delete this.items[element];
        return true
    }
    clear() {
        this.items = {};
    }
    // 获取集合的大小
    size() {
        return Object.keys(this.items).length;
    }
    // 获取集合中所有的元素
    values() {
        return Object.keys(this.items);
    }
}
```

### 使用 `Set` 类

```javascript
const set = new Set();

set.add(1);
console.log(set.values()); // 输出[1]
console.log(set.has(1)); // 输出true
console.log(set.size()); // 输出1

set.add(2);
console.log(set.values()); // 输出[1, 2]
console.log(set.has(2)); // 输出true
console.log(set.size()); // 输出2

set.delete(1);
console.log(set.values()); // 输出[2]

set.delete(2);
console.log(set.values()); // 输出[]
```

## 二、集合运算

### 集合运算涉及以下四种：

- 并集：对于给定的两个集合，返回一个包含两个集合中所有元素的新集合。
- 交集：对于给定的两个集合，返回一个包含两个集合中共有元素的新集合。
- 差集：对于给定的两个集合，返回一个包含所有存在于第一个集合且不存在于第二个集合的元素的新集合。
- 子集：验证一个给定集合是否是另一集合的子集。

### 并集

数学概念：集合 A 和集合 B 的并集表示为 A∪B

该集合定义为：A∪B={x ∣ x∈A∨x∈B}

意思是 x（元素）存在于 A 中，或 x 存在于 B 中。下图展示了并集运算：

<img src="/img/dataStructures/union.png" width="450" height="300" div align=center />

```javascript
// 并集
union(otherSet) {
    const unionSet = new Set();
    this.values().forEach(value => unionSet.add(value));
    otherSet.values().forEach(value => unionSet.add(value));
    return unionSet
}
```

```javascript
测试：
const setA = new Set();
setA.add(1);
setA.add(2);
setA.add(3);

const setB = new Set();
setB.add(3);
setB.add(4);
setB.add(5);
setB.add(6);

const unionAB = setA.union(setB);
console.log(unionAB.values());
```

### 交集

数学概念：集合 A 和集合 B的交集表示为 A∩B

该集合定义为：A∩B={x ∣ x∈A∧x∈B}

意思是 x（元素）存在于 A 中，且 x 存在于 B 中。下图展示了交集运算：

<img src="/img/dataStructures/intersection.png" width="500" height="300" div align=center />

```javascript
// 交集
intersection(otherSet) {
    const intersectionSet = new Set();
    const values = this.values();
    const otherValues = otherSet.values();
    let biggerSet = values; // 较大集合
    let smallerSet = otherValues; // 较小集合
    // 比较两个集合元素的大小并交换
    if(otherValues.length - values.length > 0) {
        biggerSet = otherValues;
        smallerSet = values;
    }
    // 循环较小集合，找出公共元素返回
    smallerSet.forEach(value => {
        if(biggerSet.includes(value)) {
            intersectionSet.add(value);
        }
    });
    return intersectionSet
}
```

```javascript
测试：
const setA = new Set();
setA.add(1);
setA.add(2);
setA.add(3);

const setB = new Set();
setB.add(2);
setB.add(3);
setB.add(4);

const intersectionAB = setA.intersection(setB);
console.log(intersectionAB.values());
```

### 差集

数学概念：集合 A 和集合 B 的差集表示为 A−B

该集合定义为：A−B={x ∣ x∈A∧x∈/B}

意思是 x（元素）存在于 A 中，且 x 不存在于 B 中。下图展示了差集运算：

<img src="/img/dataStructures/difference.png" width="500" height="300" div align=center />

```javascript
// 差集
difference(otherSet) {
    const differenceSet = new Set();
    this.values().forEach(value => {
        if (!otherSet.has(value)) {
            differenceSet.add(value);
        }
    });
    return differenceSet
}
```

```javascript
测试：
const setA = new Set();
setA.add(1);
setA.add(2);
setA.add(3);

const setB = new Set();
setB.add(2);
setB.add(3);
setB.add(4);

const differenceAB = setA.difference(setB);
console.log(differenceAB.values());
```

### 子集

数学概念：集合 A 是集合 B的子集表示为 A⊆B

该集合定义为：{x ∣ ∀x∈A⇒x∈B}

意思是集合 A 中的每一个 x（元素），也需要存在于集合 B 中。下图展示了子集运算：

<img src="/img/dataStructures/isSubsetOf.png" width="300" height="300" div align=center />

```javascript
// 子集
isSubsetOf(otherSet) {
    var values = this.values();
    // 遍历集合A中所有元素，如果发现集合A中的元素未在集合B中出现 => false
    // 如果遍历完整个集合，没有false，那么返回true
    for(let i = 0; i < values.length; i++) {
        if (!otherSet.has(values[i])) {
            return false
        }
    }
    return true
}
```

```javascript
测试：
const setA = new Set();
setA.add(1);
setA.add(2);

const setB = new Set();
setB.add(1);
setB.add(2);
setB.add(3);

const setC = new Set();
setC.add(2);
setC.add(3);
setC.add(4);

console.log(setA.isSubsetOf(setB));
console.log(setA.isSubsetOf(setC));
```
