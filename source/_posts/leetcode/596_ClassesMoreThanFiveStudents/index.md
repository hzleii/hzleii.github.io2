---
title: "596.超过5名学生的课"
description: "596.超过5名学生的课"
date: 2020-04-15
updated: 2020-04-15
categories: [LeetCode]
tags: [LeetCode]
top_img:
katex: true
---


## 题目描述

有一个 **course** 表，有：**student (学生)** 和 **class (课程)**。

请列出所有超过或等于5名学生的课。

**示例：**

```
+---------+------------+
| student | class      |
+---------+------------+
| A       | Math       |
| B       | English    |
| C       | Math       |
| D       | Biology    |
| E       | Math       |
| F       | Computer   |
| G       | Math       |
| H       | Math       |
| I       | Math       |
+---------+------------+
```

**应该输出：**

```
+---------+
| class   |
+---------+
| Math    |
+---------+
```

**说明：**

- 学生在每个课中不应该被重复计算。



## 题解

## 方法一：使用 GROUP BY 子句和子查询

**思路：**

先统计每门课程的学生数量，再从中选择超过 5 名学生的课程。

使用 **GROUP BY** 和 **COUNT** 获得每门课程的学生数量。

```sql
SELECT
    class, COUNT(DISTINCT student)
FROM
    courses
GROUP BY class
;
```

<p style="color: red; font-weight: bold;">注：使用 DISTINCT 防止在同一门课中学生被重复计算。</p>

**输出结果：**

```
+----------+----------------+
| class    | COUNT(student) |
|----------|----------------|
| Biology  | 1              |
| Computer | 1              |
| English  | 1              |
| Math     | 6              |
+----------+----------------+
```

使用上面查询结果的临时表进行子查询，筛选学生数量超过 5 的课程。

```sql
SELECT
    class
FROM
    (SELECT
        class, COUNT(DISTINCT student) AS num
    FROM
        courses
    GROUP BY class) AS temp_table
WHERE
    num >= 5
;
```


## 方法二：使用 GROUP BY 和 HAVING 条件

思路：

在 **GROUP BY** 子句后使用 **HAVING** 条件是实现子查询的一种更加简单直接的方法。

所以我们可以将上面的解决方案重写为：

```sql
SELECT
    class
FROM
    courses
GROUP BY class
HAVING COUNT(DISTINCT student) >= 5
;
```

