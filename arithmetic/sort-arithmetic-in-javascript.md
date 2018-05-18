# javascript实现常见的排序算法

<!-- vim-markdown-toc GFM -->
* [冒泡排序](#冒泡排序)
* [选择排序](#选择排序)
* [插入排序](#插入排序)
* [快速排序](#快速排序)
* [希尔排序](#希尔排序)
* [归并排序](#归并排序)

<!-- vim-markdown-toc -->

## 冒泡排序

```javascript
// 输入：[5, 6, 3, 4, 8, 0, 1, 4, 7]
// 输出：[0, 1, 3, 4, 4, 5, 6, 7, 8]
外层1：
	内层1：3 6 5 8 0 1 4 7
	内层2：0 6 5 8 3 1 4 7
外层2：
	内层1：0 5 6 8 3 1 4 7
	内层2：0 3 6 8 5 1 4 7
	内层3：0 1 6 8 5 3 4 7

// 每一次外层循环是为了找到当前最小的值
// 外层循环当前值与内层循环的值进行比较

function bubbleSort(arr) {
	for(var i = 0, l = arr.length;i < l - 1;i++) {
		for(var j = i + 1;j < l;j++) {
			if(arr[i] > arr[j]) {
				var temp = arr[i];
				arr[i] = arr[j];
				arr[j] = temp;
			}
		}
	}
	return arr;
}
```

## 选择排序

```javascript
// 每一次从待排的数据元素中选出最小或者最大的一个元素
// 存放在系列的起始位置, 直到全部待排的数据排完。

function selectSort(arr) {
	for(var i = 0;i < arr.length - 1;i++) {
		// 无序区的最小数据数组下标
		var minIndex = i;
		for(var j = i + 1;j < arr.length;j++) {
			// 在无序区查找最小数据并保存其下标
			if(arr[j] < arr[minIndex]) {
				minIndex = j;
			}
		}
		// 将最小的项移到左边
		if(minIndex != i) {
			var temp = arr[i];
			arr[i] = arr[minIndex];
			arr[minIndex] = temp;
		}
	}
	return arr;
}

```
**冒泡排序是直接交换， 而选择排序是保存下标，最后交换**

## 插入排序

```javascript
// 取第一个数字是排序好了的
// 可以理解为左边是排序好的数组
// 右边是待排序的数组

function insertSort(arr) {
	for(var i = 1;i < arr.length;i++) {
		if(arr[i - 1] > arr[i])	{
			// 记录要被插的数字
			var guard = arr[i];
			// 记录有序数组的最后一个数字的下标
			var j = i - 1;

			// 开始判断并且右移动
			while(j >= 0 && arr[j] > guard) {
				arr[j + 1] = arr[j]
				j--;
			}
			arr[j + 1] = guard;
		}
	}
	return arr;
}
```

## 快速排序

``` javascript
// 1. 在数组中，选一个元素作为基准
// 2. 所有小于基准的元素，都移到基准的左边；所有大于基准的元素，都移到基准的右边。
// 3. 对基准左边和右边的两个子集，不断重复第一步和第二步，直到所有子集只剩下一个元素为止。
function quickSort(arr) {
	if(arr.length <= 1) {
		return arr;
	}
	var pivotIndex = Math.floor(arr.length / 2);
	// 找到基准元素，并将其从数组中分离出来
	var pivot = arr.splice(pivotIndex, 1)[0];
	var leftArr = [];
	var rightArr = [];
	for(var i = 0;i < arr.length;i++) {
		if(arr[i] > pivot) {
			rightArr.push(arr[i]);
		} else {
			leftArr.push(arr[i]);
		}
	}
	return quickSort(leftArr).concat([pivot], quickSort(rightArr));
}
```


## 希尔排序

```javascript
// 希尔排序也叫增量递减排序
// 核心思想就是分组
// 然后在组里进行常规的插入排序

var arr = [10, 6, 3, 4, 8, 0, 1, 4, 7];
function shellSort(arr) {
    // 取增量
    var gap = Math.floor(arr.length / 2);

    // 只要增量大于1，就可以一直算
    while(gap > 0) {

        // 从第一个子列的最后一个数开始计算
        for(var i = gap;i < arr.length;i++) {

            // 存储当前子列的最后一个数
            var temp = arr[i];

            // 对子列进行普通的插入排序
            // i是当前子列的最后一个数的坐标
            // j等于i，j从当前子列的最后一个数往前计算
            // 这里该怎么理解呢， 我来举个例子
            // 如果数组排序到了: 8, 0, 9, 4, 7, 6, 3, 4, 10
            // 此时j为6, gap为2
            // 这样就好理解了

            for(var j = i;j >= gap && arr[j - gap] > temp; j -= gap) {

                // 将下标j对应的值改变为大的那个
                arr[j] = arr[j - gap];
            }

            // 如果中间出现了交换, j的值要变化
            // 那么相当于交换
            // 如果j的值没有变化，那么a[j]的值本身没有变化
            arr[j] = temp;
        }

        gap = Math.floor(gap / 2);
    }
    return arr;
}
```


## 归并排序

```javascript
// 归并排序的基本操作是合并两个已经排好序的数组

// 增加额外的空间，用于合并两个已经排序好的数组
function merge(left, right) {
	var result = [];
	while(left.length > 0 && right.length > 0) {
		if(left[0] < right[0]) {
			result.push(left.shift());
		} else {
			result.push(right.shift());
		}
	}
	return result.concat(left, right);
}

// 递归
function mergeSort(arr) {
	if(arr.length == 1) {
		return arr;
	}
	var middle = Math.floor(arr.length / 2);
	var left = arr.slice(0, middle)
	var right = arr.slice(middle);
	return merge(mergeSort(left), mergeSort(right));
}
```
