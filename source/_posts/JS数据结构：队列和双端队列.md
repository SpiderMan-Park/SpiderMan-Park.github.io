---
title: JS数据结构：队列和双端队列
date: 2022-09-21 16:16:39
tags:
  - 数据结构
categories:
  - 数据结构
keywords:
  - 数据结构
abbrlink: 队列和双端队列
swiper_index: 4
swiper_desc: 队列和双端队列
swiper_cover: /img/dataStructures/dataStructures.webp
top_img: /img/dataStructures/dataStructures.webp
cover: /img/dataStructures/dataStructures.webp
---

# 队列和双端队列

> 前文已经学习了栈。队列和栈非常类似，但是使用了与后进先出不同的原则。本文同样要学习双端队列的工作原理。双端队列是一种将栈的原则和队列的原则混合在一起的数据结构。

## 一、队列

> 队列是遵循**先进先出**（FIFO，也称为先来先服务）原则的一组有序的项。队列在尾部添加新元素，并从顶部移除元素。最新添加的元素必须排在队列的末尾。在计算机科学中，一个常见的例子就是打印队列。比如说我们需要打印五份文档。我们会打开每个文档，然后点击打印按钮。每个文档都会被发送至打印队列。第一个发送到打印队列的文档会首先被打印，以此类推，直到打印完所有文档。

### 创建队列

```javascript
class Queue {
  constructor() {
    this.count = 0;
    this.lowestCount = 0; // 追踪第一个元素
    this.items = {};
  }
  // 向队列尾部添加一个（或多个）新的项
  enqueue(element) {
    this.items[this.count] = element;
    this.count++;
  }
  // 移除队列的第一项（即排在队列最前面的项）并返回被移除的元素。
  dequeue() {
    if (this.isEmpty()) {
      return undefined;
    }
    const result = this.items[this.lowestCount];
    delete this.items[this.lowestCount];
    this.lowestCount++;
    return result;
  }
  // 返回队列中第一个元素——最先被添加，也将是最先被移除的元素
  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.lowestCount];
  }
  // 验证队列是否为空
  isEmpty() {
    return this.size() === 0;
  }
  // 清空队列
  clear() {
    this.items = {};
    this.count = 0;
    this.lowestCount = 0;
  }
  // 返回队列包含的元素个数
  size() {
    return this.count - this.lowestCount;
  }
  // 以字符串格式输出
  toString() {
    if (this.isEmpty()) {
      return "";
    }
    let objString = `${this.items[this.lowestCount]}`;
    for (let i = this.lowestCount + 1; i < this.count; i++) {
      objString = `${objString},${this.items[i]}`;
    }
    return objString;
  }
}
```

### 使用 `Queue` 类

```javascript
const queue = new Queue();
console.log(queue.isEmpty()); // 输出true
queue.enqueue('Summoner1');
queue.enqueue('Summoner2');
console.log(queue.toString()); // Summoner1,Summoner2
console.log(queue.size()); // 输出2
console.log(queue.isEmpty()); // 输出false
```

## 二、双端队列

>双端队列（deque）是一种允许我们同时从前端和后端添加和移除元素的特殊队列。
>
>双端队列在现实生活中的例子有电影院、餐厅中排队的队伍等。举个例子，一个刚买了票的人如果只是还需要再问一些简单的信息，就可以直接回到队伍的头部。另外，在队伍末尾的人如果赶时间，他可以直接离开队伍。
>
>在计算机科学中，双端队列的一个常见应用是存储一系列的撤销操作。每当用户在软件中进行了一个操作，该操作会被存在一个双端队列中（就像在一个栈里）。当用户点击撤销按钮时，该操作会被从双端队列中弹出，表示它被从后面移除了。在进行了预先定义的一定数量的操作后，最先进行的操作会被从双端队列的前端移除。由于双端队列同时遵守了先进先出和后进先出原则，可以说它是把队列和栈相结合的一种数据结构。

### 创建 `Deque` 类

```javascript
// 双端队列
class Deque {
  constructor() {
    this.count = 0;
    this.lowestCount = 0;
    this.items = {};
  }
  // 在双端队列前端添加新的元素
  addFront(element) {
    if (this.isEmpty()) {
      this.addBack(element);
    } else if (this.lowestCount > 0) {
      this.lowestCount--;
      this.items[this.lowestCount] = element;
    } else {
      for (let i = this.count; i > 0; i--) {
        this.items[i] = this.items[i - 1];
      }
      this.count++;
      this.items[0] = element;
    }
  }
  // 在双端队列后端添加新的元素
  addBack(element) {
    this.items[this.count] = element;
    this.count++;
  }
  // 会从双端队列前端移除第一个元素
  removeFront() {
    if (this.isEmpty()) {
      return undefined;
    }
    const result = this.items[this.lowestCount];
    delete this.items[this.lowestCount];
    this.lowestCount++;
    return result;
  }
  // 会从双端队列后端移除第一个元素
  removeBack() {
    if (this.isEmpty()) {
      return undefined;
    }
    this.count--;
    const result = this.items[this.count];
    delete this.items[this.count];
    return result;
  }
  // 返回双端队列前端的第一个元素
  peekFront() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.lowestCount];
  }
  // 返回双端队列后端的第一个元素
  peekBack() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.count - 1];
  }

  isEmpty() {
    return this.size() === 0;
  }

  clear() {
    this.items = {};
    this.count = 0;
    this.lowestCount = 0;
  }

  size() {
    return this.count - this.lowestCount;
  }

  toString() {
    if (this.isEmpty()) {
      return "";
    }
    let objString = `${this.items[this.lowestCount]}`;
    for (let i = this.lowestCount + 1; i < this.count; i++) {
      objString = `${objString},${this.items[i]}`;
    }
    return objString;
  }
}
```

### 使用 `Deque` 类

```javascript
const deque = new Deque();
console.log(deque.isEmpty()); // 输出true
deque.addBack('Summoner1');
deque.addBack('Summoner2');
console.log(deque.toString()); // Summoner1, Summoner2
deque.addBack('Summoner3');
console.log(deque.toString()); // Summoner1, Summoner2, Summoner3
console.log(deque.size()); // 输出3
console.log(deque.isEmpty()); // 输出false
deque.removeFront(); // 移除Summoner1
console.log(deque.toString()); // Summoner2, Summoner3
deque.removeBack(); // Summoner3离开
console.log(deque.toString()); // Summoner2
deque.addFront('Summoner1'); // Summoner1回来
console.log(deque.toString()); // Summoner1, Summoner2
```

## 三、使用队列和双端队列来解决问题

### 循环队列——击鼓传花游戏

> 在这个游戏中，所有人围成一个圆圈，把花尽快地传递给旁边的人。某一时刻传花停止，这个时候花在谁手里，谁就退出圆圈、结束游戏。重复这个过程，直到只剩一个人（胜者）

```javascript
function hotPotato(elementsList, num) {
  // 1.创建队列
  const queue = new Queue();
  // 人数
  const playerList = [];
  // 通过循环, 将 playerList 中的人放在队列中
  for (let i = 0; i < elementsList.length; i++) {
    queue.enqueue(elementsList[i]);
  }
  // 2.寻找最后剩下的人
  while (queue.size() > 1) {
    // 将前 num-1 中的人, 都从队列的前端取出放在队列的后端
    for (let i = 0; i < num; i++) {
      queue.enqueue(queue.dequeue());
    }
    // 将第 num 个人, 从队列中移除
    playerList.push(queue.dequeue());
  }

  return {
    players: playerList,
    winner: queue.dequeue(),
  };
}

const names = ["Summoner1", "Summoner2", "Summoner3", "Summoner4", "Summoner5"];
const result = hotPotato(names, 7);

result.players.forEach((name) => {
  console.log(`${name} 被淘汰`);
});

console.log(`最终获胜的人是: ${result.winner}`);

以上算法的输出如下:

Summoner3在击鼓传花游戏中被淘汰。
Summoner2在击鼓传花游戏中被淘汰。
Summoner5在击鼓传花游戏中被淘汰。
Summoner4在击鼓传花游戏中被淘汰。
胜利者：Summoner1
```

### 回文检查器

> 回文是正反都能读通的单词、词组、数或一系列字符的序列，例如 level 或 kayak。

```javascript
function palindromeChecker(aString) {
  if (aString === undefined || aString === null ||
    (aString !== null && aString.length === 0)) {
    return false;
  }
  const deque = new Deque();
  const lowerString = aString.toLocaleLowerCase().split(' ').join(''); // 将所有字母转化为小写，同时移除所有的空格
  let isEqual = true;
  let firstChar, lastChar;

  for (let i = 0; i < lowerString.length; i++) { // 对字符串中的所有字符执行 enqueue 操作
    deque.addBack(lowerString.charAt(i));
  }

  while (deque.size() > 1 && isEqual) { // 所有元素都在双端队列中并且首尾字符相同
    firstChar = deque.removeFront();
    lastChar = deque.removeBack();
    if (firstChar !== lastChar) {
      isEqual = false; // 要使字符串为回文，移除的两个字符必须相同
    }
  }

  return isEqual;
}

console.log('a', palindromeChecker('a'));
console.log('kayak', palindromeChecker('kayak'));
console.log('level', palindromeChecker('level'));
```

### JavaScript 任务队列

当我们在浏览器中打开新标签时，就会创建一个任务队列。这是因为每个标签都是单线程处理所有的任务，称为事件循环。浏览器要负责多个任务，如渲染 HTML、执行 JavaScript 代码、处理用户交互（用户输入、鼠标点击等）、执行和处理异步请求。

## 四、优先级队列

### 优先级队列主要考虑的问题为：

- 每个元素不再只是一个数据，还包含数据的优先级；
- 在添加数据过程中，根据优先级放入到正确位置；

### 实现

```javascript
// 保存元素和元素的优先级
class QueueElement {
  constructor(element, priority) {
    this.element = element;
    this.priority = priority;
  }
}

class PriorityQueue {
  constructor() {
    this.items = [];
    this.count = 0;
    this.lowestCount = 0; // 追踪第一个元素
  }
  enqueue(element, priority) {
    var queueElement = new QueueElement(element, priority);
    // 获取传入元素应该在正确的位置
    if (this.isEmpty()) {
      this.items.push(queueElement);
    } else {
      var added = false;
      for (var i = 0; i < this.items.length; i++) {
        // 注意: 数字越小, 优先级越高
        if (queueElement.priority < this.items[i].priority) {
          this.items.splice(i, 0, queueElement);
          added = true;
          break;
        }
      }

      // 遍历完所有的元素, 优先级都大于新插入的元素时, 就插入到最后
      if (!added) {
        this.items.push(queueElement);
      }
    }
    this.count++;
  }
  // 移除队列的第一项（即排在队列最前面的项）并返回被移除的元素。
  dequeue() {
    if (this.isEmpty()) {
      return undefined;
    }
    const result = this.items[this.lowestCount];
    delete this.items[this.lowestCount];
    this.lowestCount++;
    return result;
  }
  // 返回队列中第一个元素——最先被添加，也将是最先被移除的元素
  peek() {
    if (this.isEmpty()) {
      return undefined;
    }
    return this.items[this.lowestCount];
  }
 
  isEmpty() {
    return this.size() === 0;
  }

  clear() {
    this.items = {};
    this.count = 0;
    this.lowestCount = 0;
  }
 
  size() {
    return this.count - this.lowestCount;
  }
   
  toString() {
    if (this.isEmpty()) {
      return "";
    }
    let objString = `${this.items[this.lowestCount]}`;
    for (let i = this.lowestCount + 1; i < this.count; i++) {
      objString = `${objString},${this.items[i]}`;
    }
    return objString;
  }
}

// 创建优先级队列对象
var pQueue = new PriorityQueue();

pQueue.enqueue("aaa", 3);
pQueue.enqueue("bbb", 1);
pQueue.enqueue("ccc", 2);
pQueue.enqueue("ddd", 4);
pQueue.dequeue();
// 遍历所有的元素
var size = pQueue.size();
for (var i = 0; i < size; i++) {
  var item = pQueue.dequeue();
  console.log(item.element + "-" + item.priority);
}
```
