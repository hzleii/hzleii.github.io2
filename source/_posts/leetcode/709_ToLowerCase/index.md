---
title: "709.转换成小写字母"
description: "709.转换成小写字母"
date: 2020-04-16
updated: 2020-04-16
categories: [LeetCode]
tags: [LeetCode]
top_img:
katex: true
---


## 题目描述

**实现函数 ToLowerCase()，该函数接收一个字符串参数 str，并将该字符串中的大写字母转换成小写字母，之后返回新的字符串。**



**示例 1：**

```
输入："Hello"
输出："hello"
```

**示例 2：**

```
输入："here"
输出："here"
```

**示例 3：**

```
输入："LOVELY"
输出："lovely"
```



## 题解

## 方法一：ASCCII 码

**思路及解法**

通过 $\tt{ASCCII}$ 码表操作字符串即可。

{% tabs 代码块样式 %}
<!-- tab 写法一 -->

```java
class Solution {
    public String toLowerCase(String str) {
        if (str == null || str.length() == 0) {
            return str;
        }
        StringBuilder sb = new StringBuilder();
        for (char ch : str.toCharArray()) {
            // a-z：97-122  A-Z：65-90  0-9：48-57
            if (ch >= 'A' && ch <= 'Z') {
                sb.append((char)(ch + 32));
            } else {
                sb.append(ch);
            }
        }
        return sb.toString();
    }
}
```

<!-- endtab -->
<!-- tab 写法二 -->

```java
class Solution {
    public String toLowerCase(String str) {
        if (str == null || str.length() == 0) {
            return str;
        }
        char[] ch = str.toCharArray();
        for (int i = 0; i < str.length(); i++) {
            if (ch[i] >= 'A' && ch[i] <= 'Z') {
                ch[i] += 32;
            }
        }
        return String.valueOf(ch);
    }
}
```


<!-- endtab -->
{% endtabs %}



## 方法二：位运算

**思路及解法**

用位运算的技巧：

1. **大写变小写、小写变大写：字符 ^= 32；**
2. **大写变小写、小写变小写：字符 |= 32；**
3. **大写变大写、小写变大写：字符 &= 33。**

$\tt{ASCCII}$ 码表中大写的 $\tt{A}$ 是 $\tt{65}$，小写的 $\tt{a}$ 是 $\tt{97}$，他们的差是 $\tt{32}$；

$\tt{65 | 32}$ 转为二进制（按 8 位来算）可以得到：

$$\tt{0100 0001 \mid 0010 0000 = 0110 0001 = 97 = a}$$

**代码**

Java：

```java
class Solution {
    public String toLowerCase(String str) {
        if (str == null || str.length() == 0) {
            return str;
        }
        char[] ch = str.toCharArray();
        for (int i = 0; i < str.length(); i++) {
            ch[i] |= 32;
        }
        return String.valueOf(ch);
    }
}
```

