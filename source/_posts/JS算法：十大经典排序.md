---
title: JS算法：十大经典排序
date: 2022-09-23 23:30:28
tags:
  - 算法
categories:
  - 算法
keywords:
  - 算法
top_img: /img/algorithm/sort.jpg
cover: /img/algorithm/sort.jpg
---

# 十大经典排序

## 稳定性

**稳定**：如果待排序的序列中存在值相等的元素，经过排序之后，相等元素之间原有的先后顺序不变。 比如： a 原本在 b 前面，而 a = b，排序之后，a 仍然在 b 的前面；

**不稳定**：如果待排序的序列中存在值相等的元素，经过排序之后，相等元素之间原有的先后顺序改变。 比如：a 原本在 b 的前面，而 a = b，排序之后， a 在 b 的后面；

## 冒泡排序（Bubble Sort）

**原理**

- 冒泡排序只会操作相邻的两个数据。
- 每次冒泡操作都会对相邻的两个元素进行比较，看是否满足大小关系要求。如果不满足就让它俩互换。
- 一次冒泡会让至少一个元素移动到它应该在的位置，重复 n 次，就完成了 n 个数据的排序工作。

**特性**

- 优点：排序算法的基础，简单实用易于理解。
- 缺点：比较次数多，效率较低。

**复杂度**

- 时间复杂度：
  - 平均时间复杂度：O(n<sup>2</sup>) 
  - 最好情况：O(n)
  - 最坏情况：O(n<sup>2</sup>)
- 空间复杂度：O(1)

**在冒泡排序中，只有交换才可以改变两个元素的前后顺序。 为了保证冒泡排序算法的稳定性，当有相邻的两个元素大小相等的时候，我们不做交换，相同大小的数据在排序前后不会改变顺序。 所以冒泡排序是稳定的排序算法。**

![](/img/algorithm/BubbleSort.gif)


```javascript
const bubbleSort = arr => {
	const length = arr.length;
	if (length <= 1) return;
	// i < length - 1 是因为外层只需要 length-1 次就排好了，第 length 次比较是多余的。
	for (let i = 0; i < length - 1; i++) {
		let hasChange = false; // 提前退出冒泡循环的标志位
		// j < length - i - 1 是因为内层的 length-i-1 到 length-1 的位置已经排好了，不需要再比较一次。
		for (let j = 0; j < length - i - 1; j++) {
			if (arr[j] > arr[j + 1]) {
				const temp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = temp;
				hasChange = true; // 表示有数据交换
			}
		}

		if (!hasChange) break; // 如果 false 说明所有元素已经到位，没有数据交换，提前退出
	}
	console.log(' arr :', arr);
	console.timeEnd('冒泡排序耗时');
};
```

## 插入排序（Insertion Sort）

**原理**

- 通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

**步骤**

- 从第一个元素开始，该元素可以认为已经被排序；
- 取出下一个元素，在已经排序的元素序列中从后向前扫描；
- 如果该元素（已排序）大于新元素，将该元素移到下一位置；
- 重复步骤 3，直到找到已排序的元素小于或者等于新元素的位置；
- 将新元素插入到该位置后；
- 重复步骤 2 ~ 5。

**复杂度**

- 时间复杂度：
  - 平均时间复杂度：O(n<sup>2</sup>) 
  - 最好情况：O(n)
  - 最坏情况：O(n<sup>2</sup>)
- 空间复杂度：O(1)

**在插入排序中，对于值相同的元素，我们可以选择将后面出现的元素，插入到前面出现元素的后面，这样就可以保持原有的前后顺序不变，所以插入排序是稳定的排序算法。**

![](/img/algorithm/InsertionSort.gif)


```javascript
const insertionSort = array => {
	const len = array.length;
	if (len <= 1) return

	let preIndex, current;
	for (let i = 1; i < len; i++) {
		preIndex = i - 1; //待比较元素的下标
		current = array[i]; //当前元素
		while (preIndex >= 0 && array[preIndex] > current) {
			//前置条件之一: 待比较元素比当前元素大
			array[preIndex + 1] = array[preIndex]; //将待比较元素后移一位
			preIndex--; //游标前移一位
		}
		if (preIndex + 1 != i) {
			//避免同一个元素赋值给自身
			array[preIndex + 1] = current; //将当前元素插入预留空位
			console.log('array :', array);
		}
	}
	return array;
};
```

## 选择排序（Selection Sort）

**原理**

- 选择排序算法的实现思路有点类似插入排序，也分已排序区间和未排序区间。但是选择排序每次会从未排序区间中找到最小的元素，将其放到已排序区间的末尾。

**步骤**

- 首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置。

- 再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。

- 重复第二步，直到所有元素均排序完毕。

**复杂度**

- 时间复杂度：
  - 平均时间复杂度：O(n<sup>2</sup>)
  - 最好情况：O(n<sup>2</sup>)
  - 最坏情况：O(n<sup>2</sup>)
- 空间复杂度：O(1)

**选择排序每次都要找剩余未排序元素中的最小值，并和前面的元素交换位置，这样破坏了稳定性。所以，选择排序是一种不稳定的排序算法。**

![](/img/algorithm/SelectionSort.gif)


```javascript
const selectionSort = array => {
	const len = array.length;
	let minIndex, temp;
	for (let i = 0; i < len - 1; i++) {
		minIndex = i;
		for (let j = i + 1; j < len; j++) {
			if (array[j] < array[minIndex]) {
				// 寻找最小的数
				minIndex = j; // 将最小数的索引保存
			}
		}
		temp = array[i];
		array[i] = array[minIndex];
		array[minIndex] = temp;
		console.log('array: ', array);
	}
	return array;
};
```

## 归并排序（Merge Sort）

**原理**

- 排序一个数组，我们先把数组从中间分成前后两部分，然后对前后两部分分别排序，再将排好序的两部分合并在一起，这样整个数组就都有序了。
- 归并排序采用的是分治思想：
  - 分治，顾名思义，就是分而治之，将一个大问题分解成小的子问题来解决。小的子问题解决了，大问题也就解决了。

**复杂度**

- 时间复杂度：
  - 平均时间复杂度：O(n log n)
  - 最好情况：O(n log n)
  - 最坏情况：O(n log n)
- 空间复杂度：O(n)

**merge 方法里面的 left[0] <= right[0] ，保证了值相同的元素，在合并前后的先后顺序不变。归并排序是稳定的排序方法。**

![](/img/algorithm/MergeSort.gif)


```javascript
const mergeSort = arr => {
	//采用自上而下的递归方法
	const len = arr.length;
	if (len < 2) {
		return arr;
	}
	// length >> 1 和 Math.floor(len / 2) 等价
	let middle = Math.floor(len / 2),
		left = arr.slice(0, middle),
		right = arr.slice(middle); // 拆分为两个子数组
	return merge(mergeSort(left), mergeSort(right));
};

const merge = (left, right) => {
	const result = [];

	while (left.length && right.length) {
		// 注意: 判断的条件是小于或等于，如果只是小于，那么排序将不稳定.
		if (left[0] <= right[0]) {
			result.push(left.shift());
		} else {
			result.push(right.shift());
		}
	}

	while (left.length) result.push(left.shift());

	while (right.length) result.push(right.shift());

	return result;
};
```

## 快速排序（Quick Sort）

**原理**

- 先找到一个基准点（一般指数组的中部），然后数组被该基准点分为两部分，依次与该基准点数据比较，如果比它小，放左边；反之，放右边。
- 左右分别用一个空数组去存储比较后的数据。
- 最后递归执行上述操作，直到数组长度 <= 1;

**特性**

- 特点：快速，常用。

- 缺点：需要另外声明两个数组，浪费了内存空间资源。

**复杂度**

- 时间复杂度：
  - 平均时间复杂度：O(n log n)
  - 最好情况：O(n log n)
  - 最坏情况：O(n log n)
- 空间复杂度：O(n^2^)

**和选择排序相似，快速排序每次交换的元素都有可能不是相邻的，因此它有可能打破原来值为相同的元素之间的顺序。因此，快速排序并不稳定。**

![](/img/algorithm/QuickSort.gif)

方法一：

```javascript
// 快速排序
const quickSort = (arr, left, right) => {
	let len = arr.length,
		partitionIndex;
	left = typeof left != 'number' ? 0 : left;
	right = typeof right != 'number' ? len - 1 : right;

	if (left < right) {
		partitionIndex = partition(arr, left, right);
		quickSort(arr, left, partitionIndex - 1);
		quickSort(arr, partitionIndex + 1, right);
	}
	return arr;
};

const partition = (arr, left, right) => {
	//分区操作
	let pivot = left, //设定基准值（pivot）
		index = pivot + 1;
	for (let i = index; i <= right; i++) {
		if (arr[i] < arr[pivot]) {
			swap(arr, i, index);
			index++;
		}
	}
	swap(arr, pivot, index - 1);
	return index - 1;
};

const swap = (arr, i, j) => {
	let temp = arr[i];
	arr[i] = arr[j];
	arr[j] = temp;
};
```

方法二：

```javascript
const quickSort1 = arr => {
	if (arr.length <= 1) {
		return arr;
	}
	//取基准点
	const midIndex = Math.floor(arr.length / 2);
	//取基准点的值，splice(index,1) 则返回的是含有被删除的元素的数组。
	const valArr = arr.splice(midIndex, 1);
	const midIndexVal = valArr[0];
	const left = []; //存放比基准点小的数组
	const right = []; //存放比基准点大的数组
	//遍历数组，进行判断分配
	for (let i = 0; i < arr.length; i++) {
		if (arr[i] < midIndexVal) {
			left.push(arr[i]); //比基准点小的放在左边数组
		} else {
			right.push(arr[i]); //比基准点大的放在右边数组
		}
	}
	//递归执行以上操作，对左右两个数组进行操作，直到数组长度为 <= 1
	return quickSort1(left).concat(midIndexVal, quickSort1(right));
};

```

## 希尔排序（Shell Sort）

**原理**

- 先将整个待排序的记录序列分割成为若干子序列。
- 分别进行直接插入排序。
- 待整个序列中的记录基本有序时，再对全体记录进行依次直接插入排序。

**步骤**

- 选择一个增量序列 t1，t2，……，tk，其中 ti > tj, tk = 1；
- 按增量序列个数 k，对序列进行 k 趟排序；
- 每趟排序，根据对应的增量 ti，将待排序列分割成若干长度为 m 的子序列，分别对各子表进行直接插入排序。仅增量因子为 1 时，整个序列作为一个表来处理，表长度即为整个序列的长度。

**复杂度**

- 时间复杂度：
  - 平均时间复杂度：O(n log n)
  - 最好情况：O(n log<sup>2</sup>2<sup>2</sup>n)
  - 最坏情况：O(n log<sup>2</sup>2<sup>2</sup>n)
- 空间复杂度：O(1)

**单次直接插入排序是稳定的，它不会改变相同元素之间的相对顺序，但在多次不同的插入排序过程中，相同的元素可能在各自的插入排序中移动，可能导致相同元素相对顺序发生变化。 因此，希尔排序不稳定。**

![](/img/algorithm/ShellSort.gif)


```javascript
const shellSort = arr => {
	let len = arr.length,
		temp,
		gap = 1;
	console.time('希尔排序耗时');
	while (gap < len / 3) {
		//动态定义间隔序列
		gap = gap * 3 + 1;
	}
	for (gap; gap > 0; gap = Math.floor(gap / 3)) {
		for (let i = gap; i < len; i++) {
			temp = arr[i];
			let j = i - gap;
			for (; j >= 0 && arr[j] > temp; j -= gap) {
				arr[j + gap] = arr[j];
			}
			arr[j + gap] = temp;
			console.log('arr  :', arr);
		}
	}
	console.timeEnd('希尔排序耗时');
	return arr;
};
```

## 堆排序（Heap Sort）

**步骤**

- 用数组创建一个最大堆用作源数据。

- 在创建最大堆后，最大的值会被存储在堆的第一个位置。我们要将它替换为堆的最后一个值，将堆的大小减 1。

- 最后，我们将堆的根节点下移并重复步骤 2 直到堆的大小为 1。

我们用最大堆得到一个升序排列的数组（从最小到最大）。如果我们想要这个数组按降序排列，可以用最小堆代替。

**复杂度**

- 时间复杂度：
  - 平均时间复杂度：O(n log n)
  - 最好情况：O(n log n)
  - 最坏情况：O(n log n)
- 空间复杂度：O(1)

**因为在排序的过程，存在将堆的最后一个节点跟堆顶节点互换的操作，所以就有可能改变值相同数据的原始相对顺序。 所以，堆排序是不稳定的排序算法。**

![](/img/algorithm/HeapSort.gif)


```javascript
const heapSort = array => {
	console.time('堆排序耗时');
	// 初始化大顶堆，从第一个非叶子结点开始
	for (let i = Math.floor(array.length / 2 - 1); i >= 0; i--) {
		heapify(array, i, array.length);
	}
	// 排序，每一次 for 循环找出一个当前最大值，数组长度减一
	for (let i = Math.floor(array.length - 1); i > 0; i--) {
		// 根节点与最后一个节点交换
		swap(array, 0, i);
		// 从根节点开始调整，并且最后一个结点已经为当前最大值，不需要再参与比较，所以第三个参数为 i，即比较到最后一个结点前一个即可
		heapify(array, 0, i);
	}
	console.timeEnd('堆排序耗时');
	return array;
};

// 交换两个节点
const swap = (array, i, j) => {
	let temp = array[i];
	array[i] = array[j];
	array[j] = temp;
};

// 将 i 结点以下的堆整理为大顶堆，注意这一步实现的基础实际上是：
// 假设结点 i 以下的子堆已经是一个大顶堆，heapify 函数实现的
// 功能是实际上是：找到 结点 i 在包括结点 i 的堆中的正确位置。
// 后面将写一个 for 循环，从第一个非叶子结点开始，对每一个非叶子结点
// 都执行 heapify 操作，所以就满足了结点 i 以下的子堆已经是一大顶堆
const heapify = (array, i, length) => {
	let temp = array[i]; // 当前父节点
	// j < length 的目的是对结点 i 以下的结点全部做顺序调整
	for (let j = 2 * i + 1; j < length; j = 2 * j + 1) {
		temp = array[i]; // 将 array[i] 取出，整个过程相当于找到 array[i] 应处于的位置
		if (j + 1 < length && array[j] < array[j + 1]) {
			j++; // 找到两个孩子中较大的一个，再与父节点比较
		}
		if (temp < array[j]) {
			swap(array, i, j); // 如果父节点小于子节点:交换；否则跳出
			i = j; // 交换后，temp 的下标变为 j
		} else {
			break;
		}
	}
};
```

## 桶排序（Bucket Sort）

**桶排序是计数排序的升级版，也采用了分治思想。**

**原理**

- 将要排序的数据分到有限数量的几个有序的桶里。
- 每个桶里的数据再单独进行排序（一般用插入排序或者快速排序）。
- 桶内排完序之后，再把每个桶里的数据按照顺序依次取出，组成的序列就是有序的了。
- 桶排序利用了函数的映射关系，高效与否的关键就在于这个映射函数的确定。

- 为了使桶排序更加高效：

  - 在额外空间充足的情况下，尽量增大桶的数量。

  - 使用的映射函数能够将输入的 N 个数据均匀的分配到 K 个桶中。

**核心**

- 在于怎么把元素平均分配到每个桶里，合理的分配将大大提高排序的效率。

**步骤**

- 设置固定数量的空桶。

- 把数据放到对应的桶中。

- 对每个不为空的桶中数据进行排序。

- 拼接不为空的桶中数据，得到结果。

**复杂度**

- 时间复杂度：
  - 平均时间复杂度：O(n+k)
  - 最好情况：O(n+k)
  - 最坏情况：O(n<sup>2</sup>)
- 空间复杂度：O(n+k)

> 桶排序最好情况下使用线性时间 O(n)，桶排序的时间复杂度，取决与对各个桶之间数据进行排序的时间复杂度，因为其它部分的时间复杂度都为 O(n)。 很显然，桶划分的越小，各个桶之间的数据越少，排序所用的时间也会越少。但相应的空间消耗就会增大。

**稳定性**

- 取决于每个桶的排序方式，比如：快速排序就不稳定，归并排序就稳定。

**适用场景**

- 桶排序比较适合用在外部排序中。
- 外部排序就是数据存储在外部磁盘且数据量大，但内存有限，无法将整个数据全部加载到内存中。

**动画演示**

![](/img/algorithm/BucketSort.gif)


- 首先，设置固定数量的空桶，在这里为了方便演示，设置桶的数量为 5 个空桶

- 遍历整个数列，找到最大值为 56 ，最小值为 2 ，每个桶的范围为 （ 56 - 2 + 1 ）/ 5 = 11

- 再次遍历整个数列，按照公式 floor((数字 – 最小值) / 11) 将数字放到对应的桶中

- 比如，数字 7 代入公式 floor (( 7 – 2 ) / 11 ) = 0 放入 0 号桶

- 数字 12 代入公式 floor((12 – 2) / 11) = 0 放入 0 号桶

- 数字 56 代入公式 floor((56 – 2) / 11) = 4 放入 4 号桶

- 当向同一个索引的桶，第二次插入数据时，判断桶中已存在的数字与新插入数字的大小，按照左到右，从小到大的顺序插入（可以使用前面讲解的插入排序）实现

- 比如，插入数字 19 时， 1 号桶中已经有数字 23 ，在这里使用插入排序，让 19 排在 23 前面

- 遍历完整个数列后，合并非空的桶，按从左到右的顺序合并 0 ，1 ，2 ，3 ，4 桶。

- 这样就完成了 桶排序

```javascript
// 桶排序
const bucketSort = (array, bucketSize) => {
  if (array.length === 0) {
    return array;
  }

  console.time('桶排序耗时');
  let i = 0;
  let minValue = array[0];
  let maxValue = array[0];
  for (i = 1; i < array.length; i++) {
    if (array[i] < minValue) {
      minValue = array[i]; //输入数据的最小值
    } else if (array[i] > maxValue) {
      maxValue = array[i]; //输入数据的最大值
    }
  }

  //桶的初始化
  const DEFAULT_BUCKET_SIZE = 5; //设置桶的默认数量为 5
  bucketSize = bucketSize || DEFAULT_BUCKET_SIZE;
  const bucketCount = Math.floor((maxValue - minValue) / bucketSize) + 1;
  const buckets = new Array(bucketCount);
  for (i = 0; i < buckets.length; i++) {
    buckets[i] = [];
  }

  //利用映射函数将数据分配到各个桶中
  for (i = 0; i < array.length; i++) {
    buckets[Math.floor((array[i] - minValue) / bucketSize)].push(array[i]);
  }

  array.length = 0;
  for (i = 0; i < buckets.length; i++) {
    quickSort(buckets[i]); //对每个桶进行排序，这里使用了快速排序
    for (var j = 0; j < buckets[i].length; j++) {
      array.push(buckets[i][j]);
    }
  }
  console.timeEnd('桶排序耗时');

  return array;
};

// 快速排序
const quickSort = (arr, left, right) => {
	let len = arr.length,
		partitionIndex;
	left = typeof left != 'number' ? 0 : left;
	right = typeof right != 'number' ? len - 1 : right;

	if (left < right) {
		partitionIndex = partition(arr, left, right);
		quickSort(arr, left, partitionIndex - 1);
		quickSort(arr, partitionIndex + 1, right);
	}
	return arr;
};

const partition = (arr, left, right) => {
	//分区操作
	let pivot = left, //设定基准值（pivot）
		index = pivot + 1;
	for (let i = index; i <= right; i++) {
		if (arr[i] < arr[pivot]) {
			swap(arr, i, index);
			index++;
		}
	}
	swap(arr, pivot, index - 1);
	return index - 1;
};

const swap = (arr, i, j) => {
	let temp = arr[i];
	arr[i] = arr[j];
	arr[j] = temp;
};
```

## 计数排序（Counting Sort）

**原理**

- 找出待排序的数组中最大和最小的元素。

- 统计数组中每个值为 i 的元素出现的次数，存入新数组 countArr 的第 i 项。

- 对所有的计数累加（从 countArr 中的第一个元素开始，每一项和前一项相加）。

- 反向填充目标数组：将每个元素 i 放在新数组的第 countArr[i] 项，每放一个元素就将 countArr[i] 减去 1 。

**使用条件**

- 只能用在数据范围不大的场景中，若数据范围 k 比要排序的数据 n 大很多，就不适合用计数排序。
- 计数排序只能给非负整数排序，其他类型需要在不改变相对大小情况下，转换为非负整数。
- 比如如果考试成绩精确到小数后一位，就需要将所有分数乘以 10，转换为整数。

**复杂度**

- 时间复杂度：
  - 平均时间复杂度：O(n+k)
  - 最好情况：O(n+k)
  - 最坏情况：O(n+k)
- 空间复杂度：O(k)

**计数排序不改变相同元素之间原本相对的顺序，因此它是稳定的排序算法。**

![](/img/algorithm/CountingSort.gif)


方法一：

```javascript
const countingSort = array => {
	let len = array.length,
		result = [],
		countArr = [],
		min = (max = array[0]);
	console.time('计数排序耗时');
	for (let i = 0; i < len; i++) {
		// 获取最小，最大 值
		min = min <= array[i] ? min : array[i];
		max = max >= array[i] ? max : array[i];
		countArr[array[i]] = countArr[array[i]] ? countArr[array[i]] + 1 : 1;
	}
	console.log('countArr :', countArr);
	// 从最小值 -> 最大值,将计数逐项相加
	for (let j = min; j < max; j++) {
		countArr[j + 1] = (countArr[j + 1] || 0) + (countArr[j] || 0);
	}
	console.log('countArr 2:', countArr);
	// countArr 中,下标为 array 数值，数据为 array 数值出现次数；反向填充数据进入 result 数据
	for (let k = len - 1; k >= 0; k--) {
		// result[位置] = array 数据
		result[countArr[array[k]] - 1] = array[k];
		// 减少 countArr 数组中保存的计数
		countArr[array[k]]--;
		// console.log("array[k]:", array[k], 'countArr[array[k]] :', countArr[array[k]],)
		console.log('result:', result);
	}
	console.timeEnd('计数排序耗时');
	return result;
};
```

方法二：

```javascript
const countingSort2 = (arr, maxValue) => {
	console.time('计数排序耗时');
	maxValue = maxValue || arr.length;
	let bucket = new Array(maxValue + 1),
		sortedIndex = 0;
	(arrLen = arr.length), (bucketLen = maxValue + 1);

	for (let i = 0; i < arrLen; i++) {
		if (!bucket[arr[i]]) {
			bucket[arr[i]] = 0;
		}
		bucket[arr[i]]++;
	}

	for (let j = 0; j < bucketLen; j++) {
		while (bucket[j] > 0) {
			arr[sortedIndex++] = j;
			bucket[j]--;
		}
	}
	console.timeEnd('计数排序耗时');
	return arr;
};
```

## 基数排序（Radix Sort）

**原理**

- 基数排序是一种非比较型整数排序算法，其原理是将整数按位数切割成不同的数字，然后按每个位数分别比较。

**使用条件**

- 要求数据可以分割独立的位来比较；
- 位之间由递进关系，如果 a 数据的高位比 b 数据大，那么剩下的地位就不用比较了；
- 每一位的数据范围不能太大，要可以用线性排序，否则基数排序的时间复杂度无法做到 O(n)。

**方案**

按照优先从高位或低位来排序有两种实现方案:

- MSD：由高位为基底，先按 k1 排序分组，同一组中记录, 关键码 k1 相等，再对各组按 k2 排序分成子组, 之后，对后面的关键码继续这样的排序分组，直到按最次位关键码 kd 对各子组排序后，再将各组连接起来，便得到一个有序序列。MSD 方式适用于位数多的序列。
- LSD：由低位为基底，先从 kd 开始排序，再对 kd - 1 进行排序，依次重复，直到对 k1 排序后便得到一个有序序列。LSD 方式适用于位数少的序列。

**复杂度**

- 时间复杂度：
  - 平均时间复杂度：O(n*k)
  - 最好情况：O(n*k)
  - 最坏情况：O(n*k)
- 空间复杂度：O(n+k)

**基数排序不改变相同元素之间的相对顺序，因此它是稳定的排序算法。**

**LSD 基数排序：**

![](/img/algorithm/RadixSort.gif)


```javascript
/**
	* name: 基数排序
	* @param  array 待排序数组
	* @param  max 最大位数
	*/
const radixSort = (array, max) => {
	console.time('基数排序耗时');
	const buckets = [];
	let unit = 10,
		base = 1;
	for (let i = 0; i < max; i++, base *= 10, unit *= 10) {
		for (let j = 0; j < array.length; j++) {
			let index = ~~((array[j] % unit) / base); //依次过滤出个位，十位等等数字
			if (buckets[index] == null) {
				buckets[index] = []; //初始化桶
			}
			buckets[index].push(array[j]); //往不同桶里添加数据
		}
		let pos = 0,
			value;
		for (let j = 0, length = buckets.length; j < length; j++) {
			if (buckets[j] != null) {
				while ((value = buckets[j].shift()) != null) {
					array[pos++] = value; //将不同桶里数据挨个捞出来，为下一轮高位排序做准备，由于靠近桶底的元素排名靠前，因此从桶底先捞
				}
			}
		}
	}
	console.timeEnd('基数排序耗时');
	return array;
};
```


