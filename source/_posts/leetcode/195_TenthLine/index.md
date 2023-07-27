---
title: "195.第十行"
description: "195.第十行"
date: 2020-04-07
updated: 2020-04-07
categories: [LeetCode]
tags: [LeetCode]
top_img:
katex: true
---



## 题目描述

给定一个文本文件 file.txt，请只打印这个文件中的第十行。

**示例：**

假设 file.txt 有如下内容：

```
Line 1
Line 2
Line 3
Line 4
Line 5
Line 6
Line 7
Line 8
Line 9
Line 10
```

你的脚本应当显示第十行：

```
Line 10
```

**说明：**

1. 如果文件少于十行，你应当输出什么？
2. 至少有三种不同的解法，请尝试尽可能多的方法来解题。



## 题解

```bash
# 打印第十行
# -n: 取消默认的 sed 软件的输出, 通常和 p 一起合用
# p: 打印匹配行(通常和 -n 一起合用)
# 没有第十行, 什么也不输出
sed -n '10p' file.txt

# 打印一到十行
sed -n '1,10p' file.txt

# 打印第十行
# 变量 NR: 已经读出的记录数, 从 1 开始
# 没有第十行, 什么也不输出
# 更多变量自行百度
awk 'NR==10' file.txt

# 打印第十行
# 没有第十行, 什么也不输出
tail -n +10 file.txt | head -1
```

