---
title: JS数据结构：字典和散列表
date: 2022-09-21 20:34:59
tags:
  - 数据结构
categories:
  - 数据结构
keywords:
  - 数据结构

top_img: /img/dataStructures/dataStructures.webp
cover: /img/dataStructures/dataStructures.webp
---

# 字典和散列表

## 一、字典

>在字典中，存储的是[键，值]对，其中键名是用来查询特定元素的。字典和集合很相似，集合以[值，值]的形式存储元素，字典则是以[键，值]的形式来存储元素。字典也称作映射、符号表或关联数组。
>
>在计算机科学中，字典经常用来保存对象的引用地址。

### 创建字典类

> 在字典中，理想的情况是用字符串作为键名，值可以是任何类型（从数、字符串等原始类型，到复杂的对象）。但是，由于 JavaScript 不是强类型的语言，不能保证键一定是字符串。需要把所有作为键名传入的对象转化为字符串，使得从 Dictionary 类中搜索和获取值更简单

```javascript
// 将 key 转化为字符串, 为了把所有作为键名传入的对象转化为字符串
function defaultToString(item) {
  if (item === null) {
    return "NULL";
  } else if (item === undefined) {
    return "UNDEFINED";
  } else if (typeof item === "string" || item instanceof String) {
    return `${item}`;
  }
  return item.toString();
}
```

```javascript
class Dictionary {
  constructor(toStrFn = defaultToString) {
    this.table = {};
    this.toStrFn = toStrFn;
  }
  // 在字典中添加键值对
  set(key, value) {
    if (key != null && value != null) {
      this.table[this.toStrFn(key)] = new ValuePair(key, value);
      return true;
    }
    return false;
  }

  // 从字典中移除元素
  remove(key) {
    if (!this.hasKey(key)) return false;
    delete this.table[this.toStrFn(key)];
    return true;
  }

  // 获取所有的keys
  keys() {
    return this.keyValues().map((valuePair) => valuePair.key);
  }

  // 获取所有的value
  values() {
    return this.keyValues().map((valuePair) => valuePair.value);
  }

  // 获取所有的key和value
  keyValues() {
    return Object.values(this.table);
  }

  // 根据 key 获取value
  get(key) {
    const valuePair = this.table[this.toStrFn(key)];
    return valuePair == null ? undefined : valuePair.value;
  }

  // 如果某个键值存在于该字典中，返回 true，否则返回 false
  hasKey(key) {
    return this.table.hasOwnProperty(this.toStrFn(key));
  }

  clear() {
    this.table = {};
  }

  size() {
    return Object.keys(this.table).length;
  }

  // 字典是否为空
  isEmpty() {
    return this.size() === 0;
  }

  forEach(callbackFn) {
    const valuePairs = this.keyValues();
    for (let i = 0; i < valuePairs.length; i++) {
      const result = callbackFn(valuePairs[i].key, valuePairs[i].value);
      if (result === false) {
        break;
      }
    }
  }
}
```

### ValuePair类中设置键和值

```javascript
class ValuePair {
  constructor(key, value) {
    this.key = key;
    this.value = value;
  }
  toString() {
    return `[#${this.key}: ${this.value}]`;
  }
}
```



## 二、散列表

> 散列算法的作用是尽可能快地在数据结构中找到一个值。散列函数的作用是给定一个键值，然后返回值在表中的地址。
>
> 散列表是字典的一种实现，可以用作关联数组（对象）。

### 创建散列表

```javascript
class HashTable {
  constructor(toStrFn = defaultToString) {
    this.toStrFn = toStrFn;
    this.table = {};
  }
  // 散列函数
  loseloseHashCode(key) {
    if (typeof key == "number") {
      return key;
    }
    const tableKey = this.toStrFn(key);
    let hash = 0;
    for (let i = 0; i < tableKey.length; i++) {
      hash += tableKey.charCodeAt(i);
    }
    return hash % 37;
  }
    
  hashCode(key) {
    return this.loseloseHashCode(key);
  }
  // 将键和值加入散列表
  put(key, value) {
    if (key != null && value != null) {
      const position = this.hashCode(key);
      this.table[position] = new ValuePair(key, value);
      return true;
    }
    return false;
  }
  // 从散列表中获取一个值
  get(key) {
    const valuePair = this.table[this.hashCode(key)];
    return valuePair == null ? undefined : valuePair.value;
  }
  // 从散列表中移除一个值
  remove(key) {
    const hash = this.hashCode(key);
    const valuePair = this.table[hash];
    if (valuePair != null) {
      delete this.table[hash];
      return true;
    }
    return false;
  }
  getTable() {
    return this.table;
  }
  isEmpty() {
    return this.size() === 0;
  }
  size() {
    return Object.keys(this.table).length;
  }
  clear() {
    this.table = {};
  }
  toString() {
    if (this.isEmpty()) {
      return "";
    }
    const keys = Object.keys(this.table);
    let objString = `{${keys[0]} => ${this.table[keys[0]].toString()}}`;
    for (let i = 1; i < keys.length; i++) {
      objString = `${objString},{${keys[i]} => ${this.table[
        keys[i]
      ].toString()}}`;
    }
    return objString;
  }
}
```

### 处理散列表中的冲突

> 有时候，一些键会有相同的散列值。不同的值在散列表中对应相同位置的时候，称其为**冲突**。
>
> 使用一个数据结构来保存数据的目的显然不是丢失这些数据，而是通过某种方法将它们全部保存起来。因此，当这种情况发生的时候就要去解决。处理冲突有两种方法：分离链接、线性探查，这两种方法只需要重写put、get、remove三种方法就可以了。

#### 1、分离链接

分离链接法包括为散列表的每一个位置创建一个链表并将元素存储在里面。它是解决冲突的最简单的方法，但是在 HashTable 实例之外还需要额外的存储空间。

- put方法

```javascript
put(key, value) {
  if (key != null && value != null) {
    const position = this.hashCode(key);
    if (this.table[position] == null) { // 验证要加入新元素的位置是否已经被占据
      this.table[position] = new LinkedList(); // 如果是第一次向该位置加入元素，那么就要在该位置上初始化一个 LinkedList 类的实例
    }
    this.table[position].push(new ValuePair(key, value));
    return true;
  }
  return false;
}
```

- get方法

```javascript
get(key) {
  const position = this.hashCode(key);
  const linkedList = this.table[position]; // 验证在特定的位置上是否有元素存在
  if (linkedList != null && !linkedList.isEmpty()) { // 检验是否存在 linkedList 实例
    let current = linkedList.getHead(); // 获取链表表头的引用
    while (current != null) { // 链表的头部迭代到尾部
      if (current.element.key === key) {
        return current.element.value;
      }
      current = current.next;
    }
  }
  return undefined;
}
```

- remove方法

```javascript
remove(key) {
  const position = this.hashCode(key);
  const linkedList = this.table[position];
  if (linkedList != null && !linkedList.isEmpty()) {
    let current = linkedList.getHead();
    while (current != null) {
      if (current.element.key === key) {
        linkedList.remove(current.element);
        if (linkedList.isEmpty()) {
          delete this.table[position];
        }
        return true;
      }
      current = current.next;
    }
  }
  return false;
}
```

#### 2、线性勘察

因为它处理冲突的方法是将元素直接存储到表中，而不是在单独的数据结构中。

当想向表中某个位置添加一个新元素的时候，如果索引为 position 的位置已经被占据了，就尝试 position+1 的位置。如果 position+1 的位置也被占据了，就尝试 position+2 的位置，以此类推，直到在散列表中找到一个空闲的位置。想象一下，有一个已经包含一些元素的散列表，我们想要添加一个新的键和值。我们计算这个新键的 hash，并检查散列表中对应的位置是否被占据。如果没有，我们就将该值添加到正确的位置。如果被占据了，我们就迭代散列表，直到找到一个空闲的位置。

- put方法

```javascript
put(key, value) {
  if (key != null && value != null) {
    const position = this.hashCode(key);
    if (this.table[position] == null) { // {1}
      this.table[position] = new ValuePair(key, value); // {2}
    } else {
      let index = position + 1; // {3}
      while (this.table[index] != null) { // {4}
        index++; // {5}
      }
      this.table[index] = new ValuePair(key, value); // {6}
    }
    return true;
  }
  return false;
}
```

- get方法

```javascript
get(key) {
  const position = this.hashCode(key);
  if (this.table[position] != null) { // {1}
    if (this.table[position].key === key) { // {2}
      return this.table[position].value; // {3}
    }
    let index = position + 1; // {4}
    while (this.table[index] != null && this.table[index].key !== key) { // {5}
      index++;
    }
    if (this.table[index] != null && this.table[index].key === key) { // {6}
      return this.table[position].value; // {7}
    }
  }
  return undefined; // {8}
}
```

- remove方法

```javascript
remove(key) {
  const position = this.hashCode(key);
  if (this.table[position] != null) {
    if (this.table[position].key === key) {
      delete this.table[position]; // {1}
      this.verifyRemoveSideEffect(key, position); // 需要验证删除操作是否有副作用。如果有，就需要将冲突的元素移动至一个之前的位置，这样就不会产生空位置
      return true;
    }
    let index = position + 1;
    while (this.table[index] != null && this.table[index].key !== key ) {
      index++;
    }
    if (this.table[index] != null && this.table[index].key === key) {
      delete this.table[index]; // {3}
      this.verifyRemoveSideEffect(key, index); // 需要验证删除操作是否有副作用。如果有，就需要将冲突的元素移动至一个之前的位置，这样就不会产生空位置
      return true;
    }
  }
  return false;
}
```

> 由于我们不知道在散列表的不同位置上是否存在具有相同 hash 的元素，需要验证删除操作是否有副作用。要实现此功能，需要创建一个工具方法，声明如下：

```javascript
verifyRemoveSideEffect(key, removedPosition) { // 被删除的 key 和该 key 被删除的位置
  const hash = this.hashCode(key); // 获取被删除的 key 的 hash 值
  let index = removedPosition + 1; // 从下一个位置开始迭代散列表
  while (this.table[index] != null) { // 找到一个空位置
    const posHash = this.hashCode(this.table[index].key); // 计算当前位置上元素的 hash 值
    if (posHash <= hash || posHash <= removedPosition) { // 如果当前元素的 hash 值小于或等于原始的 hash 值或当前元素的 hash 值小于或等于 removedPosition（也就是上一个被移除 key 的 hash 值）
      this.table[removedPosition] = this.table[index]; // 需要将当前元素移动至 removedPosition 的位置
      delete this.table[index];
      removedPosition = index; // removedPosition 更新为当前的 index
    }
    index++;
  }
}
```

### 创建更好的散列函数（没有冲突）

前面实现的 lose lose 散列函数并不是一个表现良好的散列函数，因为它会产生太多的冲突。一个表现良好的散列函数是由几个方面构成的：**插入和检索元素的时间（即性能），以及较低的冲突可能性**。

另一个可以实现的、比 lose lose 更好的散列函数是 djb2。

```javascript
djb2HashCode(key) {
  const tableKey = this.toStrFn(key);
  let hash = 5381;
  for (let i = 0; i < tableKey.length; i++) {
    hash = (hash * 33) + tableKey.charCodeAt(i);
  }
  return hash % 1013;
}
```