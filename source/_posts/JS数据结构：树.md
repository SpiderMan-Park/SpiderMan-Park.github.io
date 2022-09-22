---
title: JS数据结构：树
date: 2022-09-21 21:33:45
tags:
  - 数据结构
categories:
  - 数据结构
keywords:
  - 数据结构
swiper_index: 6
swiper_desc: 树
swiper_cover: /img/dataStructures/dataStructures.webp
top_img: /img/dataStructures/dataStructures.webp
cover: /img/dataStructures/dataStructures.webp
---

# 树

## 一、二叉树和二叉搜索树

>二叉树中的节点最多只能有两个子节点：一个是左侧子节点，另一个是右侧子节点。这个定义有助于我们写出更高效地在树中插入、查找和删除节点的算法。二叉树在计算机科学中的应用非常广泛。
>
>二叉搜索树（BST）是二叉树的一种，但是只允许你在左侧节点存储（比父节点）小的值，在右侧节点存储（比父节点）大的值。

### 二叉树的特性

- **一个二叉树第i层的最大节点数为：2^(i - 1)，i >= 1**;

- **深度为k的二叉树有最大节点总数为：2^k - 1，k >= 1**;

- **对任何非空二叉树T，若n0表示叶节点的个数、n2是度为2的非叶节点个数，那么两者满足关系n0 = n2 + 1**。

### 树和二叉树的三个主要差别

- **树的节点个数至少为1，而二叉树的节点个数可以为0**

- **树中节点的最大度数(节点数量)没有限制,而二叉树的节点的最大度数为2**

- **树的节点没有左右之分，而二叉树的节点有左右之分**

### 二叉树分类

**二叉树分为完全二叉树(complete binary tree)和满二叉树(full binary tree)**

- **满二叉树：一棵深度为k且有2^k - 1个节点的二叉树称为满二叉树**

- **完全二叉树：完全二叉树是指最后一层左边是满的，右边可能满也可能不满，然后其余层都是满的二叉树称为完全二叉树(满二叉树也是一种完全二叉树)**

![](/img/dataStructures/treeType.webp)


### 创建 `BinarySearchTree` 类

```javascript
// 二叉搜索树中的每个节点
class Node {
  constructor(key) {
    this.key = key;
    this.left = null;
    this.right = null;
  }
}
```

```javascript
class binarySearchTree {
  constructor() {
    this.root = null;
  }
}
```

### 向BST中插入一个键

验证插入操作是否是特殊情况：

- 判断插入的树节点是否为第一个节点
- 节点添加到根节点以外的其他位置

```javascript
// 插入一个键
  insert(key) {
    // 根据 key 创建根节点
    var newNode = new Node(key);
    // 判断根节点是否存在
    if (this.root == null) {
      this.root = newNode;
    } else {
      // 存在根节点：递归比较新节点和原根节点
      this.insertNode(this.root, newNode);
    }
  }
```

```javascript
// 插入——递归比较节点
  insertNode(node, newNode) {
    // 新插入的节点和左右节点都要比较
    if (newNode.key < node.key) {
      // 向左查找
      // 判断左子树是否存在
      if (node.left == null) {
        node.left = newNode;
      } else {
        this.insertNode(node.left, newNode);
      }
    } else {
      // 向右查找
      // 判断右子树是否存在
      if (node.right == null) {
        node.right = newNode;
      } else {
        this.insertNode(node.right, newNode);
      }
    }
  }
```

## 二、BST遍历

> 访问树的所有节点有三种方式：中序、先序和后序。

- 中序遍历

```javascript
// 中序遍历: 左 -> 根 -> 右
  inOrderTraversal(handle) {
    this.inOrderTraversalNode(this.root, handle);
  }
  // 中序遍历递归调用节点
  inOrderTraversalNode(node, handle) {
    if (node != null) {
      this.inOrderTraversalNode(node.left, handle);
      handle(node.key);
      this.inOrderTraversalNode(node.right, handle);
    }
  }
```

- 先序遍历

```javascript
// 先序遍历: 根 -> 左 -> 右
  preOrderTraversal(handle) {
    this.preOrderTraversalNode(this.root, handle);
  }
  // 先序遍历递归调用节点
  preOrderTraversalNode(node, handle) {
    if (node != null) {
      handle(node.key);
      this.preOrderTraversalNode(node.left, handle);
      this.preOrderTraversalNode(node.right, handle);
    }
  }
```

- 后序遍历

```javascript
// 后序遍历: 左 -> 右 -> 根
  postOrderTraversal(handle) {
    this.postOrderTraversalNode(this.root, handle);
  }
  // 后序遍历递归调用节点
  postOrderTraversalNode(node, handle) {
    if (node != null) {
      this.postOrderTraversalNode(node.left, handle);
      this.postOrderTraversalNode(node.right, handle);
      handle(node.key);
    }
  }
```

- 测试

```javascript
var bst = new binarySearchTree();
bst.insert(11);
bst.insert(7);
bst.insert(15);
bst.insert(5);
bst.insert(3);
bst.insert(9);
bst.insert(8);
bst.insert(10);
bst.insert(13);
bst.insert(12);
bst.insert(14);
bst.insert(20);
bst.insert(18);
bst.insert(25);
bst.insert(6);

var resultString = "";
bst.postOrderTraversal(function (key) {
  resultString += key + " ";
});
console.log(resultString);
```

## 三、搜索树中的值

### 搜索最小值和最大值

```javascript
// 最大值
  max() {
    var node = this.root;
    while (node.left != null) {
      node = node.right;
    }
    return node.key;
  }
  // 最小值
  min() {
    var node = this.root;
    while (node.right != null) {
      node = node.left;
    }
    return node.key;
  }
```

### 搜索一个特定的值

```javascript
// 查找任意一个键（递归）
  search(key) {
    return this.searchNode(this.root, key);
  }
  searchNode(node, key) {
    if (node == null) {
      return false;
    }
    if (key < node.key) {
      return this.searchNode(node.left, key);
    } else if (key > node.key) {
      return this.searchNode(node.right, key);
    } else {
      return true;
    }
  }
```

### 移除一个节点

```javascript
// 删除一个节点
  remove(key) {
    var current = this.root;
    var parent = null;
    var isLeftChild = true;

    // 查找节点
    while (current.key != key) {
      parent = current;
      if (key < current.key) {
        isLeftChild = true;
        current = current.left;
      } else {
        isLeftChild = false;
        current = current.right;
      }

      // 如果current已经指向null, 那么说明没有找到要删除的数据
      if (current == null) return false;
    }

    // 删除的节点是叶子结点
    if (current.left == null && current.right == null) {
      if (current == this.root) {
        this.root == null;
      } else if (isLeftChild) {
        parent.left = null;
      } else {
        parent.right = null;
      }
    }

    // 删除有一个子节点的节点
    else if (current.right == null) {
      if (current == this.root) {
        this.root = current.left;
      } else if (isLeftChild) {
        parent.left = current.left;
      } else {
        parent.right = current.left;
      }
    } else if (current.left == null) {
      if (current == this.root) {
        this.root = current.right;
      } else if (isLeftChild) {
        parent.left = current.right;
      } else {
        parent.right = current.right;
      }
    }

    // 删除有两个节点的节点
    else {
      // 获取后继节点
      var successor = this.getSuccessor(current);

      // 判断是否是根节点
      if (current == this.root) {
        this.root = successor;
      } else if (isLeftChild) {
        parent.left = successor;
      } else {
        parent.right = successor;
      }

      // 将删除节点的左子树赋值给successor
      successor.left = current.left;
    }
  }

  // 找后继方法
  getSuccessor(delNode) {
    var successor = delNode;
    var current = delNode.right; // 要从右子树开始找
    var successorParent = delNode;

    // 寻找节点
    while (current != null) {
      successorParent = successor;
      successor = current;
      current = current.left;
    }
    // 判断寻找的后继节点是否直接就是delNode的right节点
    if (successor != delNode.right) {
      successorParent.left = successor.right;
      successor.right = delNode.right;
    }

    return successor;
  }
```