---
title: Java-快速排序
date: 2020-07-23
lastmod: 2020-07-23
math: true
description: Java版快速排序(待优化)
categories: [算法]
tags:  [Java]
---




# Java-快速排序（时间复杂度：O(N*logN) ）



## 简要说明
快速排序由C. A. R. Hoare在1960年提出。

它的基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序。

它采用了一种分治的策略，通常称其为分治法(Divide-and-ConquerMethod)。

最坏运行情况是 O(n²)



## 动图展示

![快速排序](https://online-education-headimg.oss-cn-beijing.aliyuncs.com/%E5%8D%9A%E5%AE%A2/%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87/quickSort.gif)



## 代码展示

```java
public class QuickSort {

	public static void main(String[] args) {
		
		int[] arr = {3, 44, 38, 5, 47, 15, 36, 26, 27, 2, 46, 4, 19, 50, 48};
		
		quickSort(arr, 0, arr.length - 1);
		
		System.out.println(Arrays.toString(arr));

	}
	
	/*
	 * 传入 3 个参数
	 * 1, 数组 arr
	 * 2, 排序开始的位置, 左边 left
	 * 3, 排序结束的位置, 右边 right
	 */
	public static void quickSort(int[] arr, int left, int right) {
		// 进行判断, 如果左边索引比右边索引大, 不合法, 直接用 return 结束这个方法
		if (left > right) {
			return;
		}
		
		// 定义变量保存基准数
		int base = arr[left];
		// 定义变量 i, 指向最左边
		int i = left;
		// 定义变量 j, 指向最右边
		int j = right;
		
		// 当 i 和 j 不相遇的时候, 在循环中进行检索
		while (i != j) {
			// 先由 j 从右往左检索比基准数小的, 如果检索到比基准数小的, 就停下
			// 如果检索到比基准数大的或者相等的, 就继续检索
			while (arr[j] >= base && i < j) {
				j--;	// j从右往左移动
			}
			
			// i 从左往右检索
			while (arr[i] <= base && i < j) {
				i++;	// i 从左往右移动
			}
			
			// 代码走到这里, i 和 j 都停下了, 然后交换 i 和 j 的位置的元素
			int temp = arr[i];
			arr[i] = arr[j];
			arr[j] = temp;
		}
		
		/*
		 * 如果上面 while 循环的条件不成立, 会跳出循环, 往下执行
		 * 如果这个条件不成立, 说明 i 和 j 相遇了
		 * 如果 i 和 j 相遇了, 就交换基准数这个元素和相遇位置的元素
		 * 把相遇位置的元素赋值给基准数这个位置的元素
		 */
		arr[left] = arr[i];
		
		// 把基准数赋值给相遇位置的元素
		arr[i] = base;
		
		// 基准数在这里就归位了, 左边的数字比他小, 右边的数字比他大
		
		// 然后排基准数的左边
		quickSort(arr, left, i - 1);
		
		// 然后排基准数的右边
		quickSort(arr, j + 1, right);
	}

}
```