---
title: Java-插入排序
date: 2020-07-22 21:01:24
lastmod: 2020-07-22 21:01:24
math: true
description: Java-插入排序
categories: [算法]
tags:  [Java]
---



# Java-插入排序（复杂度为O(n2) ）



## 简要说明

插入排序是一种最简单的排序方法，它的基本思想是从数组中逐个元素作为插入元素，和前面的进行比较，小的即插入前面。



## 动图展示

![插入排序](https://online-education-headimg.oss-cn-beijing.aliyuncs.com/%E5%8D%9A%E5%AE%A2/%E5%8D%9A%E5%AE%A2%E6%96%87%E7%AB%A0%E5%9B%BE%E7%89%87/insertionSort.gif)



## 代码展示

```java
public class InsertSort {

    public static void main(String[] args) {

        int[] arr = {3, 44, 38, 5, 47, 15, 36, 26, 27, 2, 46, 4, 19, 50, 48};

        insertSort(arr);

        System.out.println(Arrays.toString(arr));

    }

    /**
     * 插入排序：从数组中逐个元素作为插入元素，和前面的进行比较，小的即插入前面。
     * 前面的已经是排完序的了。
     *
     * @param arr 待排序的数组
     */
    public static void insertSort(int[] arr) {
        for (int i = 1; i < arr.length; i++) {
            // 插入的下标，默认是前一位
            int insertIndex = i - 1;
            // 插入的值
            int insertValue = arr[i];
            // 插入的值和前面的进行比较，小则插入。排序：从小到大，改大于号则排序为从大到小
            while (insertIndex >= 0 && insertValue < arr[insertIndex]){
                // 交换，将小的放前面，保证前面是已经排序后的
                arr[insertIndex + 1] = arr[insertIndex];
                insertIndex --;
            }

            if(insertIndex != i - 1) {
                arr[insertIndex + 1] = insertValue;
            }

            System.out.println("第" + i + "轮：" + Arrays.toString(arr));

        }
    }
}
```
