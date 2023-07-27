---
title: "191.位1的个数"
description: "191.位1的个数"
date: 2020-04-06
updated: 2020-04-06
categories: [LeetCode]
tags: [LeetCode]
top_img:
katex: true
---



## 题目描述

**编写一个函数，输入是一个无符号整数（以二进制串的形式），返回其二进制表达式中数字位数为 '1' 的个数（也被称为[汉明重量](https://baike.baidu.com/item/汉明重量)）。**

**提示：**

- 请注意，在某些语言（如 Java）中，没有无符号整数类型。在这种情况下，输入和输出都将被指定为有符号整数类型，并且不应影响您的实现，因为无论整数是有符号的还是无符号的，其内部的二进制表示形式都是相同的。
- 在 Java 中，编译器使用[二进制补码](https://baike.baidu.com/item/二进制补码/5295284)记法来表示有符号整数。因此，在上面的{% span red,  示例3 %}中，输入表示有符号整数$\tt {-3}$。

**进阶：**

- 如果多次调用这个函数，你将如何优化你的算法？

**示例 1：**

```
输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```

**示例 2：**

```
输入：00000000000000000000000010000000
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。
```

**示例 3：**

```
输入：11111111111111111111111111111101
输出：31
解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
```

**提示：**

- 输入必须是长度为$\tt {32}$的二进制串



## 题解

## 方法一：循环检查二进制位

**思路及解法**

我们可以直接循环检查给定整数 $\tt{n}$ 的二进制位的每一位是否为 $\tt{1}$。

具体代码中，当检查第 $\tt{i}$ 位时，我们可以让 $\tt{n}$ 与 $\tt{2^i}$ 进行与运算，当且仅当 $\tt{n}$ 的第 $\tt{i}$ 位为 $\tt{1}$ 时，运算结果不为 $\tt{0}$。

**代码展示：**

{% tabs 代码块样式 %}


<!-- tab Java -->

```java
public class Solution {
    public int hammingWeight(int n) {
        int ret = 0;
        for (int i = 0; i < 32; i++) {
            if ((n & (1 << i)) != 0) {
                ret++;
            }
        }
        return ret;
    }
}
```


<!-- endtab -->
<!-- tab Python -->


```python
class Solution:
    def hammingWeight(self, n: int) -> int:
        ret = sum(1 for i in range(32) if n & (1 << i)) 
        return ret
```


<!-- endtab -->
<!-- tab C -->


```c
int hammingWeight(uint32_t n) {
    int ret = 0;
    for (int i = 0; i < 32; i++) {
        if (n & (1u << i)) {
            ret++;
        }
    }
    return ret;
}
```


<!-- endtab -->
<!-- tab JavaScript -->


```javascript
var hammingWeight = function(n) {
    let ret = 0;
    for (let i = 0; i < 32; i++) {
        if ((n & (1 << i)) !== 0) {
            ret++;
        }
    }
    return ret;
};
```


<!-- endtab -->
{% endtabs %}


**复杂度分析：**

- 时间复杂度：$\rm{O(n)}$。其中 $\tt{n}$ 是 $\tt{int}$ 型的二进制位数，$\tt{k = 32}$。我们需要检查 $\tt{n}$ 的二进制位的每一位，一共需要检查 $\tt{32}$位。
- 空间复杂度：$\rm{O(1)}$。我们只需要常数的空间保存若干变量。



## 方法二：位运算优化

**思路及解法**

我们可以把前面的算法进行优化。我们不再检查数字的每一个位，而是不断把数字最后一个$\tt{1}$反转，并把答案 $\tt {+1}$。当数字变成 $\tt{0}$ 的时候，我们就知道它没有 $\tt{1}$ 的位了，此时返回答案。

这里关键的想法是对于任意数字 $\tt{n}$，将 $\tt{n}$ 和 $\tt{n-1}$ 做与运算，会把最后一个 $\tt{1}$ 的位变成 $\tt{0}$。为什么？考虑 $\tt{n}$ 和 $\tt{n-1}$ 的二进制表示。

在二进制表示中，数字 $\tt{n}$ 中最低位的 $\tt{1}$ 总是对应 $\tt{n-1}$ 中的 $\tt{0}$。因此将 $\tt{n}$ 和 $\tt{n-1}$ 做与运算总是能把 $\tt{n}$ 中最低位的 $\tt{1}$ 变成 $\tt{0}$，并保持其他位置不变。

使用这个小技巧，代码就变得非常简单。

**代码展示：**


{% tabs 代码块样式 %}
<!-- tab Java -->

```java
public class Solution {
    public int hammingWeight(int n) {
        int ret = 0;
        while (n != 0) {
            n &= n - 1;
            ret++;
        }
        return ret;
    }
}
```


<!-- endtab -->
<!-- tab Python -->


```python
class Solution:
    def hammingWeight(self, n: int) -> int:
        ret = 0
        while n:
            n &= n - 1
            ret += 1
        return ret
```


<!-- endtab -->
<!-- tab C -->


```c
int hammingWeight(uint32_t n) {
    int ret = 0;
    while (n) {
        n &= n - 1;
        ret++;
    }
    return ret;
}
```


<!-- endtab -->
<!-- tab JavaScript -->


```javascript
var hammingWeight = function(n) {
    let ret = 0;
    while (n) {
        n &= n - 1;
        ret++;
    }
    return ret;
};
```


<!-- endtab -->
{% endtabs %}

**复杂度分析：**

- 时间复杂度：$\rm{O(\log n)}$。循环次数等于$\tt{n}$的二进制位中$\tt{1}$的个数，最坏情况下$\tt{n}$的二进制位全部为$\tt{1}$。我们需要循环$\tt{\log n}$次。
- 空间复杂度：$\rm{O(1)}$。我们只需要常数的空间保存若干变量。

