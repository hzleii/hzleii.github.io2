---
title: "283.移动零"
description: "283.移动零"
date: 2020-04-12
updated: 2020-04-12
categories: [LeetCode]
tags: [LeetCode]
top_img:
katex: true
---



## 题目描述

**给定一个数组 nums，编写一个函数将所有的 0 移动到数组的末尾，同时保持非零元素的相对顺序。**

**示例 1：**

```
输入：[0, 1, 0, 3, 12]
输出：[1, 3, 12, 0, 0]
```

**说明：**

1. 必须在原数组上操作，不能拷贝额外的数组。
2. 尽量减少操作次数。



## 题解

## 方法一：双指针

**思路及解法**

使用双指针，左指针指向当前已经处理好的序列的尾部，右指针只想待处理序列的头部。

右指针不断向右移动，每次右指针指向非零数，则将左右指针对应的数交换，同时左指针右移。

注意到以下性质：

1. **左指针左边均为非零数；**
2. **右指针左边到左指针处均为零。**



**代码**

{% tabs 代码块样式 %}
<!-- tab Java -->


```java
class Solution {
    public void moveZeroes(int[] nums) {
        int n = nums.length, left = 0, right = 0;
        while (right < n) {
            if (nums[right] != 0) {
                swap(nums, left, right);
                left++;
            }
            right++;
        }
    }

    public void swap(int[] nums, int left, int right) {
        int temp = nums[left];
        nums[left] = nums[right];
        nums[right] = temp;
    }
}
```


<!-- endtab -->
<!-- tab Python -->


```python
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        n = len(nums)
        left = right = 0
        while right < n:
            if nums[right] != 0:
                nums[left], nums[right] = nums[right], nums[left]
                left += 1
            right += 1
```


<!-- endtab -->
<!-- tab C -->


```c
void swap(int *a, int *b) {
    int t = *a;
    *a = *b, *b = t;
}

void moveZeroes(int *nums, int numsSize) {
    int left = 0, right = 0;
    while (right < numsSize) {
        if (nums[right]) {
            swap(nums + left, nums + right);
            left++;
        }
        right++;
    }
}
```


<!-- endtab -->
{% endtabs %}


**复杂度分析：**

- 时间复杂度：$\rm{O(n)}$，其中 $ \tt{n}$ 是序列长度。每个位置至多被遍历两次。
- 空间复杂度：$\rm{O(1)}$。只需要常数的空间存放若干变量。

