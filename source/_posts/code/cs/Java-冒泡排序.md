---
title: Java-冒泡排序
date: 2020-07-21
lastmod: 2020-07-21
math: true
description: Java版冒泡排序(待优化)
categories: [算法]
tags:  [Java]
---



# Java-冒泡排序



## 简要说明

依次比较两个相邻的元素



## 动图展示

![冒泡排序](https://online-education-headimg.oss-cn-beijing.aliyuncs.com/%E5%8D%9A%E5%AE%A2/%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87/bubbleSort.gif)



## 代码展示

```java
public class BubbleSort {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		
		int[] arr = {3, 44, 38, 5, 47, 15, 36, 26, 27, 2, 46, 4, 19, 50, 48};
		
		for (int i = 0; i < arr.length - 1; i++) {
			for (int j = 0; j < arr.length - i - 1; j++) {
				if (arr[j] > arr[j + 1]) {
					int temp = arr[j];
					arr[j] = arr[j + 1];
					arr[j + 1]  = temp;
				}
			}
		}
		System.out.println(Arrays.toString(arr));

	}

}

```
