---
title: Java-选择排序
date: 2020-07-25
lastmod: 2020-07-25
math: true
description: Java-选择排序
categories: [算法]
tags:  [Java]
---



# Java-选择排序（时间复杂度为O(n²) ）



## 简要说明
首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置。

再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。



## 动图展示
![选择排序](https://online-education-headimg.oss-cn-beijing.aliyuncs.com/%E5%8D%9A%E5%AE%A2/%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87/selectionSort.gif)



## 代码展示

```java
public class SelectionSort {

    public static void main(String[] args) {

        int[] arr = {3, 44, 38, 5, 47, 15, 36, 26, 27, 2, 46, 4, 19, 50, 48};

        selectionSort(arr);

        System.out.println(Arrays.toString(arr));

    }

    /**
     * 选择排序：前一个与后面逐个进行比较，将最小的放到前面
     * @param arr 待排序的数组
     */
    public static void selectionSort(int[] arr) {
        for (int i = 0; i < arr.length - 1; i++) {
            // 最小值的索引，默认是前面元素
            int minIndex = i;
            // 最小值，默认是前面元素
            int minValue = arr[i];

            // 与后面的元素进行比较
            for (int j = i + 1; j < arr.length; j++) {
                // 比较大小，得到最小的值、下标
                if (minValue > arr[j]){
                    minIndex = j;
                    minValue = arr[j];
                }
            }
            // 如果最小值不是默认的元素则交换
            if (minIndex != i){
                arr[minIndex] = arr[i];
                arr[i] = minValue;
            }
        }
    }
}
```