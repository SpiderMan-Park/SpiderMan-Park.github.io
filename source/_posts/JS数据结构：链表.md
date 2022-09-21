---
title: JS数据结构：链表
date: 2022-09-21 17:23:34
tags:
  - 数据结构
categories:
  - 数据结构
keywords:
  - 数据结构
swiper_index: 5
swiper_desc: 链表
swiper_cover: /img/dataStructures/dataStructures.webp
top_img: /img/dataStructures/dataStructures.webp
cover: /img/dataStructures/dataStructures.webp
---

# 链表

## 一、链表

链表存储有序的元素集合，但不同于数组，链表中的元素在内存中并不是连续放置的。每个元素由一个存储元素本身的节点和一个指向下一个元素的引用（也称指针或链接）组成。下图展示了一个链表的结构：


![](/img/dataStructures/LinkedList.png)


相对于传统的数组，链表的一个好处在于，添加或移除元素的时候不需要移动其他元素。然而，链表需要使用指针，因此实现链表时需要额外注意。在数组中，我们可以直接访问任何位置的任何元素，而要想访问链表中间的一个元素，则需要从起点（表头）开始迭代链表直到找到所需的元素。

### 创建链表

```javascript
// 保存单向链表每个节点信息
Node {
  constructor(element, next) {
    this.element = element;
    this.next = next;
  }
}
```

```javascript
class LinkedList {
  constructor() {
    // 链表中的属性
    this.count = 0;
    this.head = null;
  }
  // 链表尾部添加一个新元素
  push(element) {
    // 1.根据新元素创建节点
    const node = new Node(element);
    // 2.判断原来链表是否为空
    // 2.1 链表为空
    if (this.head == null) {
      this.head = node;
    } else {
      // 2.2 链表不为空
      var current = this.head;
      while (current.next != null) {
        // 2.3保存当前找到的节点
        current = current.next;
      }
      // 3.找到最后一项, 将其next赋值为新添加的node节点
      current.next = node;
    }
    // 4.链表长度加1
    this.count++;
  }

  // 向链表的特定位置插入一个新元素
  insert(element, index) {
    // 1.检测越界问题: 越界插入失败
    if (index >= 0 && index <= this.count) {
      const node = new Node(element);
      // 2.判断是否在链表索引为 0 的位置插入节点
      if (index === 0) {
        const current = this.head;
        node.next = current; // 2.1新添加的节点指向原位置的第一个节点
        this.head = node; // 2.2让 head 指向新插入的节点
      } else {
        // 3.插入其他位置
        // 3.1找到插入位置的前一个节点
        const previous = this.getElementAt(index - 1);
        // 3.2让新节点指向原位置
        node.next = previous.next;
        // 3.3让前一个节点指向新插入的节点
        previous.next = node;
      }
      this.count++;
      return true;
    }
    return false;
  }

  // 返回链表中特定位置的元素。如果链表中不存在这样的元素，则返回 undefined
  getElementAt(index) {
    if (index >= 0 && index <= this.count) {
      let node = this.head;
      for (let i = 0; i < index && node != null; i++) {
        node = node.next;
      }
      return node;
    }
    return undefined;
  }

  // 返回元素在链表中的索引。如果链表中没有该元素则返回 -1
  indexOf(element) {
    let current = this.head;
    let index = 0;
    // 循环查找链表中是否存在相同的值
    while (current) {
      if (current.element === element) {
        return index;
      }
      // 逐个向后查找
      current = current.next;
      index++;
    }
    // 没找到返回 -1
    return -1;
  }

  // 修改链表中任意位置的值
  update(index, newElement) {
    if (index >= 0 && index <= this.count) {
      this.getElementAt(index).element = newElement;
      return true;
    }
    return false;
  }

  // 从链表的特定位置移除一个元素
  removeAt(index) {
    if (index >= 0 && index < this.count) {
      let current = this.head;
      if (index === 0) {
        this.head = current.next;
      } else {
        const previous = this.getElementAt(index - 1);
        current = previous.next;
        previous.next = current.next;
      }
      this.count--;
      return current.element;
    }
    return undefined;
  }

  // 从链表中移除一个元素
  remove(element) {
    const index = this.indexOf(element);
    return this.removeAt(index);
  }
  isEmpty() {
    return this.size() === 0;
  }
  size() {
    return this.count;
  }
  // 获取链表头节点的元素
  getHead() {
    return this.head;
  }
  clear() {
    this.head = null;
    this.count = 0;
  }
  toString() {
    if (this.head == null) {
      return "";
    }
    let objString = `${this.head.element}`;
    let current = this.head.next;
    for (let i = 1; i < this.size() && current != null; i++) {
      objString = `${objString},${current.element}`;
      current = current.next;
    }
    return objString;
  }
}
```

### 使用 `LinkedList` 类

```javascript
var list = new LinkedList();

list.push(10);
list.push(15);
list.push(20);
list.push(25);
list.insert(30, 4);
list.insert(5, 0);
```

## 二、双向链表

双向链表和单向链表的区别在于，在链表中，一个节点只有链向下一个节点的链接；而在双向链表中，链接是双向的：一个链向下一个元素，另一个链向前一个元素，如下图所示：


![](/img/dataStructures/DoublyLinkedList.png)



### 创建双向链表

```javascript
// 保存双向链表每个节点信息
DoublyNode extends Node {
  constructor(element, next, prev) {
    super(element, next);
    this.prev = prev;
  }
}
```

```javascript
class DoublyLinkedList extends LinkedList {
  constructor(count, head) {
    super(count, head);
    this.tail = null;
  }
  push(element) {
    const node = new DoublyNode(element);
    if (this.head == null) {
      this.head = node;
      this.tail = node;
    } else {
      this.tail.next = node;
      node.prev = this.tail;
      this.tail = node;
    }
    this.count++;
  }

  // 从任意位置插入元素
  insert(element, index) {
    // 判断越界
    if (index < 0 || index > this.count) return false;
    const node = new DoublyNode(element);
    // 断插入的位置
    if (index === 0) {
      // 在第一个位置插入数据
      // 判断链表是否为空
      if (this.head == null) {
        this.head = node;
        this.tail = node;
      } else {
        this.head.prev = node;
        node.next = this.head;
        this.head = node;
      }
    } else if (index === this.count) {
      // 插入到最后的情况
      this.tail.next = node;
      node.prev = this.tail;
      this.tail = node;
    } else {
      // 在中间位置插入数据
      var position = 0;
      var current = this.head;
      var previous = null;

      // 查找正确的位置
      while (position++ < index) {
        previous = current;
        current = current.next;
      }

      // 交换节点的指向顺序
      node.next = current;
      node.prev = previous;
      current.prev = node;
      previous.next = node;
    }
    this.count++;

    return true;
  }

  remove(element) {
    var index = this.indexOf(element);
    return this.removeAt(index);
  }

  // 从任意位置移除元素
  removeAt(index) {
    // 1.判断越界的问题
    if (index < 0 || index >= this.length) return null;

    // 2.判断移除的位置
    var current = this.head;
    if (index === 0) {
      if (this.count == 1) {
        this.head = null;
        this.tail = null;
      } else {
        this.head = this.head.next;
        this.head.prev = null;
      }
    } else if (index === this.count - 1) {
      current = this.tail;
      this.tail = this.tail.prev;
      this.tail.next = null;
    } else {
      var position = 0;
      var previous = null;

      while (position++ < index) {
        previous = current;
        current = current.next;
      }

      previous.next = current.next;
      current.next.prev = previous;
    }

    this.count--;

    return current.element;
  }
  // 获取头部节点
  getHead() {
    return this.head;
  }
  // 获取尾部节点
  getTail() {
    return this.tail;
  }
  clear() {
    super.clear();
    this.tail = null;
  }
  // 返回链表中特定位置的元素。如果链表中不存在这样的元素，则返回 undefined
  getElementAt(index) {
    if (index >= 0 && index <= this.count) {
      let node = this.head;
      for (let i = 0; i < index && node != null; i++) {
        node = node.next;
      }
      return node;
    }
    return undefined;
  }
  // 根据元素获取在链表中的位置
  indexOf(element) {
    var current = this.head;
    var index = 0;
    while (current) {
      if (current.element === element) {
        return index;
      }
      index++;
      current = current.next;
    }
    return -1;
  }
  // 正向遍历
  forwardString() {
    var current = this.head;
    var forwardStr = "";
    while (current) {
      forwardStr += "," + current.element;
      current = current.next;
    }
    return forwardStr.slice(1);
  }
  // 反向遍历
  reverseString() {
    var current = this.tail;
    var reverseStr = "";
    while (current) {
      reverseStr += "," + current.element;
      current = current.prev;
    }
    return reverseStr.slice(1);
  }
  toString() {
    return this.forwardString();
  }
}
```

## 三、创建 `StackLinkedList` 类

> 在栈的实现内部使用链表数据结构会更加简单，因为不需要重新创建这些代码，也使代码的可读性更好。 

```javascript
class StackLinkedList {
  constructor() {
    this.items = new DoublyLinkedList(); // 可以直接获取头尾的元素，减少过程消耗，时间复杂度为O(1)
  }
  push(element) {
    this.items.push(element);
  }
  pop() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items.removeAt(this.size() - 1);
  }
  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items.getElementAt(this.size() - 1).element;
  }
  isEmpty() {
    return this.items.isEmpty();
  }
  size() {
    return this.items.size();
  }
  clear() {
    this.items.clear();
  }
  toString() {
    return this.items.toString();
  }
}
```