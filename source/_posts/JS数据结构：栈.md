---
title: JS数据结构：栈
date: 2022-09-21 15:36:23
tags:
  - 数据结构
categories:
  - 数据结构
keywords:
  - 数据结构
top_img: /img/dataStructures/dataStructures.webp
cover: /img/dataStructures/dataStructures.webp
---

# 栈

> 栈是一种遵从**后进先出**（LIFO）原则的有序集合。新添加或待删除的元素都保存在栈的同一端，称作栈顶，另一端就叫栈底。在栈里，新元素都靠近栈顶，旧元素都接近栈底。栈也被用在编程语言的编译器和内存中保存变量、方法调用等，也被用于浏览器历史记录（浏览器的返回按钮）。

## 一、创建一个基于数组的栈

```javascript
// 栈类
class Stack {
  constructor() {
    // 栈中的属性
    this.items = [];
  }
  // 栈相关的方法

  // 压栈
  push(element) {
    this.items.push(element);
  }
  // 出栈
  pop() {
    return this.items.pop();
  }
  // peek
  peek() {
    return this.items[this.items.length - 1];
  }
  // 判断栈中的元素是否为空
  isEmpty() {
    return this.items.length === 0;
  }
  // 获取栈中元素的个数
  size() {
    return this.items.length;
  }
  // 清空当前栈
  clear() {
    this.items = [];
  }
  // 以数组格式输出
  toArray() {
    return this.items;
  }
  // 以字符串格式输出
  toString() {
    return this.items.toString();
  }
}
```

### 使用 `Stack` 类

```javascript
var stack = new Stack();
stack.push("aaa");
stack.push("bbb");
console.log(stack.peek());
console.log(stack.size());
console.log(stack.isEmpty());
```

## 二、创建一个基于 JavaScript 对象的 `Stack` 类

> 我们可以使用一个对象来存储所有的栈元素，保证它们的顺序并且遵循**LIFO**原则。

```javascript
class Stack {
  constructor() {
    this.count = 0;
    this.items = {};
  }
  push(element) {
    this.items[this.count] = element;
    this.count++;
  }
  pop() {
    if (this.isEmpty()) {
      return undefined;
    }
    this.count--;
    const result = this.items[this.count];
    delete this.items[this.count];
    return result;
  }
  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.count - 1];
  }
  isEmpty() {
    return this.count === 0;
  }
  size() {
    return this.count;
  }
  clear() {
    /* while (!this.isEmpty()) {
          this.pop();
        } */
    this.items = {};
    this.count = 0;
  }
  toString() {
    if (this.isEmpty()) {
      return "";
    }
    let objString = `${this.items[0]}`;
    for (let i = 1; i < this.count; i++) {
      objString = `${objString},${this.items[i]}`;
    }
    return objString;
  }
}
```

## 三、两种方法的区别

- 数组大部分方法的时间复杂度都为O(n)，数组中的元素是一个有序集合，为了保证元素排列有序，它会占用更多的内存空间。

- 对象可以通过key直接访问到目标元素时间复杂度为O(1)，我们可以直接目标元素进行操作，速度明显比数组快了很多倍。

## 四、用栈解决问题

栈的实际应用非常广泛，现实生活中，我们主要使用十进制。但在计算科学中，二进制非常重要，因为计算机里的所有内容都是用二进制数字表示的（0 和 1）。没有十进制和二进制相互转化的能力，与计算机交流就很困难。我们用栈来实现一个基数为 2～36之间的任意进制转换的算法。

```javascript
function baseConverter(decNumber, base) {
  const remStack = new Stack();
  const digits = '0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ';
  let number = decNumber;
  let rem;
  let baseString = '';

  if (!(base >= 2 && base <= 36)) {
    return '';
  }

  while (number > 0) {
    rem = Math.floor(number % base); //获得余数
    remStack.push(rem);
    number = Math.floor(number / base); // 返回整数部分
  }

  while (!remStack.isEmpty()) {
    baseString += digits[remStack.pop()]; // 元素出栈并连成字符串
  }

  return baseString;
}
```