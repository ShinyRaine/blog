---
title: JS中的排序算法
date: 2016-07-25 10:02:31
tags: [javascript, 算法]
---
## 插入排序
对于少量元素排序是一个有效算法。从第二个元素开始，将元素逐个插入正确的位置。插入的方法是用while循环。

```javascript
var insertSort = function(arr) {
	var key, i
	for (var j = 1; j < arr.length; j++) {
		key = arr[j]
		i = j - 1
		while (i > -1 && arr[i] > key) {
			arr[i + 1] = arr[i]	
			i = i - 1
		}
		arr[i + 1] = key
	}
	return arr
}
```
<!--more-->
插入排序运行时间依赖于输入长度和它们已被排序的程度。
最好的情况，输入数组已被排序，运行时间：an+b，是n的线性函数；最坏情况，输入数组反序，运行时间：an^2+bn+c,是 n的2次函数。

## 归并排序
这个写了好久。。原理是把数组逐步分成更小的数组，最后就分成了只有一个元素的数组，再去合并这些已排序小数组。

```javascript
var merge = function(arr1,arr2) {
	var i = 0,j = 0,r = arr1.length + arr2.length,arr=[]
	arr1[arr1.length] = Number.POSITIVE_INFINITY
	arr2[arr2.length] = Number.POSITIVE_INFINITY
	for (var k = 0; k < r; k++) {
		if (arr1[i] <= arr2[j]) {
			arr.push(arr1[i])
			i++
		}else{
			arr.push(arr2[j])
			j++
		}
	}
	return arr
}
var mergeSort = function(arr) {
	if (arr.length == 1) {
		return arr
	}
	var q = Math.floor(arr.length / 2)
	var arr1 = arr.slice(0, q),
		arr2 = arr.slice(q)
	var result = merge(mergeSort(arr1),mergeSort(arr2))
	return result
}
```
最坏运行时间用递归式表示：T(n) = 2`*`T(2/n) + Θ(n) (n>1);T(n) = Θ(1) (n=1)。可利用主定理证明：时间复杂度为O(nlgn)。

## 堆排序
首先将输入数组建成最大堆。因为最大元素总在根节点处，每次将根节点提出放到正确的位置。

```js
var exchange = function (arr, i, j) {
	var temp = arr[i]
	arr[i] = arr[j]
	arr[j] = temp
}
var heapify = function (arr, i, size) {
	var l = i*2+1,
		r = l+1,
		max = i
	if (l < size && arr[l] > arr[max]) {
		max = l
	}
	if (r < size && arr[r] > arr[max]) {
		max = r
	}
	if (max != i) {
		exchange(arr, i, max)
		heapify(arr, max, size)
	}
}
var build = function (arr) {
	var s = Math.floor( arr.length/2 ) - 1
	for (var i = s; i >= 0 ; i--) {
		heapify(arr,i,arr.length)
	}
}
var heapSort = function (arr) {
	build(arr)
	for (var i = arr.length-1; i > 0; i--) {
		exchange(arr, 0, i)
		heapify(arr, 0, i)
	}
	return arr
}
```
## 快速排序
将数组arr[p...r]划分成子数组arr[p...q-1]和arr[q+1...r]，使arr[q+1...r]中每个元素都小于arr[q]，且arr[q]小于或等于arr[p...q-1]中的每个元素，递归调用对子数组进行排序。就是递归地找到中值元素，然后合并。

```js
var exchange = function (arr, i, j) {
	var temp = arr[i]
	arr[i] = arr[j]
	arr[j] = temp
}
var quickSort = function (arr) {

	var sort = function(arr, p, r) {
		if (p<r) {
			q = partition(arr, p, r)
			sort(arr, p, q-1)
			sort(arr, q+1, r)
		}
	}

	sort(arr,0,arr.length-1)
	
}

var partition = function(arr, p, r) {
	var x = arr[r]
	var i = p - 1
	for (var j = p; j < r; j++) {
		if (arr[j] < x) {
			i++
			exchange(arr,i,j)
		}
	}
	exchange(arr,i+1,r)
	return i+1
}
```
时间复杂度为O(nlgn)

## 计数排序
对每一个输入元素x，确定小于x的元素个数，把x放到它在输出数组的位置。

```js
var countSort = function (arr) {
	var len = arr.length
	var c = [], result = []
	var max = 0 
	for (var i = 0; i < len; i++) {
		if (arr[i] > max) {
			max = arr[i]
		}
	}
	for (var i = 0; i < max+1; i++) {
		c[i] = 0
	}
	for (var i = 0; i < len; i++) {
		c[arr[i]] = c[arr[i]] + 1
	}
	for (var i = 1; i < max+1; i++) {
		c[i] = c[i] + c[i-1]
	}
	for (var i = len - 1; i >= 0; i--) {
		result[c[arr[i]] - 1] = arr[i]
		c[arr[i]] = c[arr[i]] - 1

	}
	return result
}
```
第一个循环找出最大的元素max；第二个循环设置中介数组c为length为max+1、元素均为0的数组；第三个循环让c[i]的值为输入数组arr中i的个数；第四个循环将c[i]的值等于arr中有多少元素是小于等于i的；最后一个循环从后向前将arr中的元素放入输出数组的相应位置中。

## 基数排序
这个是从个位开始按位排序。先按个位数数字大小排序，再十位数，百位数、、、这样。由于较小位是排好的，所以要求用的排序方式是“稳定的”，也就是说具有相同值的元素在输出数组中的相对次序与它们在输入数组中的相对次序相同。上面的计数排序是稳定的，所以这里对某个位上的数进行排序时用的是计数排序的思路。

```js
var raidxSort = function (arr) {
	var d = 1
	// 求最高位
	for (var i = 0; i < arr.length; i++) {
		if (arr[i].toString().length > d) {
			d = arr[i].toString().length
		}
	}
	for (var i = 1; i <= d; i++) {
		var digitArr = arr.map(function(x) {
			var digit = x.toString().length
			return Number(x.toString()[digit - i])  || 0
		})
		// 这里用前面的计数排序的方法，因为它是稳定的
		var max = 0, c = [], result=[], len = arr.length
		for (var j = 0; j < len; j++) {
			if (digitArr[j] > max) {
				max = digitArr[j]
			}
		}
		for (var j = 0; j < max+1; j++) {
			c[j] = 0
		}
		for (var j = 0; j < len; j++) {
			c[digitArr[j]] = c[digitArr[j]] + 1
		}
		for (var j = 1; j < max+1; j++) {
			c[j] = c[j] + c[j-1]
		}
		for (var j = len - 1; j >= 0; j--) {
			result[c[digitArr[j]] - 1] = arr[j]
			c[digitArr[j]] = c[digitArr[j]] - 1
		}
		for (var j = 0; j < arr.length; j++) {
			arr[j] = result[j]
		}
	}
	console.log(arr)
}
```
## 桶排序

<没错我看的是算法导论,剩下一个的没时间了回学校更吧>