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
最好的情况，输入数组已被排序，运行时间：an+b，是n的线性函数；最坏情况，输入数组反序，运行时间：an^2+bn+c,s n的2次函数。时间复杂度Θ(n^2)。

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
	var	q = Math.floor(arr.length / 2)
	var arr1 = arr.slice(0, q),
		arr2 = arr.slice(q)
	var result = merge(mergeSort(arr1),mergeSort(arr2))
	return result
}
```
最坏运行时间用递归式表示：T(n) = 2`*`T(2/n) + Θ(n) (n>1);T(n) = Θ(1) (n=1)。可利用主定理证明：时间复杂度为Θ(nlgn)。
主定理：令a>=1，b>1是常数，f(n)是一个函数，T(n)是定义在非负整数上的递归式：T(n) = a`*`T(b/n) + f(n),则T(n)有如下渐进界：
1.若对于某个常数c>0，有f(n)=O(n^logb(a-c)),则T(n)=Θ(nlogb(a))
2.若f(n)=Θ(nlogb(a))，则T(n)=Θ(nlogb(a)`*`lgn)
3.若对于某个常数c>0，有f(n)=Ω(n^logb(a+c))，且对某个常数d<1和收益足够大的n有af(n/b)<=cf(n),则T(n)=Θ(f(n))
描述符号不清楚可以看下图
![主定理图](http://img0.ph.126.net/gYOYfDQ1n6d4qPPi5EZ77w==/6631505371327614306.png)
未完待续...