---
title: MySQL 行转列，列转行
date: 2021-06-08
description: MySQL 行转列，列转行
tags: ["MySQL"]
categories: ["MySQL"]
---



## 行转列

即将原本同一列下多行的不同内容作为多个字段，输出对应内容

### 测试数据

建表：

```sql
DROP TABLE IF EXISTS TB_SCORE;

CREATE TABLE TB_SCORE (
	ID INT ( 11 ) NOT NULL auto_increment,
	USER_ID VARCHAR ( 20 ) NOT NULL COMMENT '用户id',
	SUBJECT VARCHAR ( 20 ) COMMENT '科目',
	SCORE DOUBLE COMMENT '成绩',
PRIMARY KEY ( ID ) 
) ENGINE = INNODB DEFAULT CHARSET = utf8;
```

插入数据：

```sql
INSERT INTO TB_SCORE(USER_ID, SUBJECT, SCORE) VALUES ('1001', '语文', 90);
INSERT INTO TB_SCORE(USER_ID, SUBJECT, SCORE) VALUES ('1001', '数学', 92);
INSERT INTO TB_SCORE(USER_ID, SUBJECT, SCORE) VALUES ('1001', '英语', 80);
INSERT INTO TB_SCORE(USER_ID, SUBJECT, SCORE) VALUES ('1002', '语文', 88);
INSERT INTO TB_SCORE(USER_ID, SUBJECT, SCORE) VALUES ('1002', '数学', 90);
INSERT INTO TB_SCORE(USER_ID, SUBJECT, SCORE) VALUES ('1002', '英语', 75);
INSERT INTO TB_SCORE(USER_ID, SUBJECT, SCORE) VALUES ('1003', '语文', 70);
INSERT INTO TB_SCORE(USER_ID, SUBJECT, SCORE) VALUES ('1003', '数学', 85);
INSERT INTO TB_SCORE(USER_ID, SUBJECT, SCORE) VALUES ('1003', '英语', 90);
INSERT INTO TB_SCORE(USER_ID, SUBJECT, SCORE) VALUES ('1003', '政治', 82);
```

查询表中数据：

```sql
SELECT * FROM TB_SCORE
```

| ID   | USER_ID | SUBJECT | SCORE |
| ---- | ------- | ------- | ----- |
| 1    | 1001    | 语文    | 90    |
| 2    | 1001    | 数学    | 92    |
| 3    | 1001    | 英语    | 80    |
| 4    | 1002    | 语文    | 88    |
| 5    | 1002    | 数学    | 90    |
| 6    | 1002    | 英语    | 75    |
| 7    | 1003    | 语文    | 70    |
| 8    | 1003    | 数学    | 85    |
| 9    | 1003    | 英语    | 90    |
| 10   | 1003    | 政治    | 82    |

### 1. 使用`case...when...then`

```sql
SELECT USER_ID,
	SUM(CASE `SUBJECT` WHEN '语文' THEN SCORE ELSE 0 END) as '语文',
	SUM(CASE `SUBJECT` WHEN '数学' THEN SCORE ELSE 0 END) as '数学',
	SUM(CASE `SUBJECT` WHEN '英语' THEN SCORE ELSE 0 END) as '英语',
	SUM(CASE `SUBJECT` WHEN '政治' THEN SCORE ELSE 0 END) as '政治' 
FROM TB_SCORE 
GROUP BY USER_ID
```

### 2. 使用`IF()`

```sql
SELECT USER_ID,
	SUM(IF(`SUBJECT` = '语文', SCORE, 0)) as '语文',
	SUM(IF(`SUBJECT` = '数学', SCORE, 0)) as '数学',
	SUM(IF(`SUBJECT` = '英语', SCORE, 0)) as '英语',
	SUM(IF(`SUBJECT` = '政治', SCORE, 0)) as '政治' 
FROM TB_SCORE 
GROUP BY USER_ID
```

两者运行结果都为：

| USER_ID | 语文 | 数学 | 英语 | 政治 |
| ------- | ---- | ---- | ---- | ---- |
| 1001    | 90   | 92   | 80   | 0    |
| 1002    | 88   | 90   | 75   | 0    |
| 1003    | 70   | 85   | 90   | 82   |

可以看出，这里行转列是将原来的`SUBJECT`字段的多行内容选出来，作为结果集中的不同列，并根据`USER_ID`进行分组显示对应的`SCORE`。

{% note info %}

1. `SUM()`是为了能够使用`GROUP BY`根据`USER_ID`进行分组，**因为**每一个`USER_ID`对应的`SUBJECT=语文`的**记录只有一条**，所以`SUM()`的值就等于对应那一条记录的score的值。
2. 假如`USER_ID = '1001' AND SUBJECT = '语文'`的记录有两条，则此时`SUM()`的值将会是这两条记录的和，同理，使用`MAX()`的值将会是这两条记录里面最大的一个。但是正常情况下，一个`USER`对应一个`SUBJECT`只有一个分数，<span style="color:red; font-weight: bold;">因此使用 SUM()、MAX()、MIN()、AVG()等聚合函数都可以达到行转列的效果</span>
3. `IF('SUBJECT' = '语文', score, 0)`作为条件，即对所有`SUBJECT='语文'`的记录的`SCORE`字段进行`SUM(), MAX(), MIN(), AVG()`操作，如果`SCORE`没有值则默认为`0`。

{% endnote %}



### 3. 利用`SUM(IF())`生成列 + `WITH ROLLUP` 生成汇总行，并利用`IFNULL`将汇总行标题显示为**Total**

```sql
SELECT IFNULL(USER_ID, 'total') AS USER_ID,
	SUM(IF(`SUBJECT` = '语文', SCORE, 0)) AS 语文,
	SUM(IF(`SUBJECT` = '数学', SCORE, 0)) AS 数学,
	SUM(IF(`SUBJECT` = '英语', SCORE, 0)) AS 英语,
	SUM(IF(`SUBJECT` = '政治', SCORE, 0)) AS 政治,
	SUM(IF(`SUBJECT` = 'total', SCORE, 0)) AS total
FROM(
	SELECT USER_ID, IFNULL(`SUBJECT`, 'total') AS `SUBJECT`, SUM(SCORE) AS SCORE
	FROM TB_SCORE
	GROUP BY USER_ID, `SUBJECT`
	WITH ROLLUP
	HAVING USER_ID IS NOT NULL
)AS A 
GROUP BY USER_ID
WITH ROLLUP;
```

运行结果：

| USER_ID | 语文 | 数学 | 英语 | 政治 | total |
| ------- | ---- | ---- | ---- | ---- | ----- |
| 1001    | 90   | 92   | 80   | 0    | 262   |
| 1002    | 88   | 90   | 75   | 0    | 253   |
| 1003    | 70   | 85   | 90   | 82   | 327   |
| total   | 248  | 267  | 245  | 82   | 842   |

### 4. 利用`SUM(IF())`生成列 + `UNION`生成汇总行，并利用`IFNULL`将汇总行标题显示为TOTAL

```sql
SELECT IFNULL(USER_ID, 'TOTAL') AS USER_ID,
	SUM(IF(`SUBJECT` = '语文', SCORE, 0)) AS 语文,
	SUM(IF(`SUBJECT` = '数学', SCORE, 0)) AS 数学,
	SUM(IF(`SUBJECT` = '英语', SCORE, 0)) AS 英语,
	SUM(IF(`SUBJECT` = '政治', SCORE, 0)) AS 政治,
	SUM(IF(`SUBJECT` = 'total', SCORE, 0)) AS TOTAL
FROM(
    SELECT USER_ID, IFNULL(`SUBJECT`, 'total') AS `SUBJECT`, SUM(SCORE) AS SCORE
    FROM TB_SCORE
    GROUP BY USER_ID, `SUBJECT`
    WITH ROLLUP
    HAVING USER_ID IS NOT NULL
)AS A 
GROUP BY USER_ID
WITH ROLLUP;
```

运行结果：

| USER_ID | 语文 | 数学 | 英语 | 政治 | TOTAL |
| ------- | ---- | ---- | ---- | ---- | ----- |
| 1001    | 90   | 92   | 80   | 0    | 262   |
| 1002    | 88   | 90   | 75   | 0    | 253   |
| 1003    | 70   | 85   | 90   | 82   | 327   |
| TOTAL   | 248  | 267  | 245  | 82   | 842   |

### 5. 利用`SUM(IF())`生成列，直接生成结果，不再利用子查询

```sql
SELECT IFNULL(USER_ID, 'TOTAL') AS USER_ID,
	SUM(IF(`SUBJECT` = '语文', SCORE, 0)) AS 语文,
	SUM(IF(`SUBJECT` = '数学', SCORE, 0)) AS 数学,
	SUM(IF(`SUBJECT` = '英语', SCORE, 0)) AS 英语,
	SUM(IF(`SUBJECT` = '政治', SCORE, 0)) AS 政治,
	SUM(SCORE) AS TOTAL 
FROM TB_SCORE
GROUP BY USER_ID WITH ROLLUP;
```

运行结果：

| USER_ID | 语文 | 数学 | 英语 | 政治 | TOTAL |
| ------- | ---- | ---- | ---- | ---- | ----- |
| 1001    | 90   | 92   | 80   | 0    | 262   |
| 1002    | 88   | 90   | 75   | 0    | 253   |
| 1003    | 70   | 85   | 90   | 82   | 327   |
| TOTAL   | 248  | 267  | 245  | 82   | 842   |

### 6. 合并字段显示：利用`GROUP_CONCAT()`

```sql
SELECT
	USER_ID,
	GROUP_CONCAT( `SUBJECT`, ":", SCORE ) AS 成绩 
FROM
	TB_SCORE 
GROUP BY
	USER_ID
```

运行结果：

| USER_ID | 成绩                            |
| ------- | ------------------------------- |
| 1001    | 语文:90,数学:92,英语:80         |
| 1002    | 语文:88,数学:90,英语:75         |
| 1003    | 语文:70,数学:85,英语:90,政治:82 |

`GROUP_CONCAT()`，手册上说明：该函数返回带有来自一个组的连接的`非NULL`值的字符串结果。
比较抽象，难以理解。通俗点理解，其实是这样的：`GROUP_CONCAT()`会计算哪些行属于同一组，将属于同一组的列显示出来。要返回哪些列，由函数参数(就是字段名)决定。分组必须有个标准，就是根据`GROUP BY`指定的列进行分组。

## 列转行

### 测试数据

建表：

```sql
DROP TABLE IF EXISTS TB_SCORE;

CREATE TABLE TB_SCORE (
	ID INT ( 11 ) NOT NULL auto_increment,
	USER_ID VARCHAR ( 20 ) NOT NULL COMMENT '用户id',
	CN_SCORE DOUBLE COMMENT '语文成绩',
	MATH_SCORE DOUBLE COMMENT '数学成绩',
	EN_SCORE DOUBLE COMMENT '英语成绩',
	PO_SCORE DOUBLE COMMENT '政治成绩',
	PRIMARY KEY ( ID ) 
) ENGINE = INNODB DEFAULT CHARSET = utf8;
```

插入数据：

```sql
INSERT INTO TB_SCORE ( USER_ID, CN_SCORE, MATH_SCORE, EN_SCORE, PO_SCORE )
VALUES ( '1001', 90, 92, 80, 0 );
INSERT INTO TB_SCORE ( USER_ID, CN_SCORE, MATH_SCORE, EN_SCORE, PO_SCORE )
VALUES ( '1002', 88, 90, 75.5, 0 );
INSERT INTO TB_SCORE ( USER_ID, CN_SCORE, MATH_SCORE, EN_SCORE, PO_SCORE )
VALUES ( '1003', 70, 85, 90, 82 );
```

查询表中数据：

```sql
SELECT * FROM TB_SCORE;
```

| ID   | USER_ID | CN_SCORE | MATH_SCORE | EN_SCORE | PO_SCORE |
| ---- | ------- | -------- | ---------- | -------- | -------- |
| 1    | 1001    | 90       | 92         | 80       | 0        |
| 2    | 1002    | 88       | 90         | 75.5     | 0        |
| 3    | 1003    | 70       | 85         | 90       | 82       |

### UNION ALL

本质是将 USER_ID 的每个科目分数分散成一条记录显示出来。

```sql
SELECT USER_ID, '语文' AS COURSE, CN_SCORE AS SCORE FROM TB_SCORE
UNION ALL
SELECT USER_ID, '数学' AS COURSE, MATH_SCORE AS SCORE FROM TB_SCORE
UNION ALL
SELECT USER_ID, '英语' AS COURSE, EN_SCORE AS SCORE FROM TB_SCORE
UNION ALL
SELECT USER_ID, '政治' AS COURSE, PO_SCORE AS SCORE FROM TB_SCORE
ORDER BY USER_ID
```

运行结果：

| USER_ID | COURSE | SCORE |
| ------- | ------ | ----- |
| 1001    | 语文   | 90    |
| 1001    | 数学   | 92    |
| 1001    | 英语   | 80    |
| 1001    | 政治   | 0     |
| 1002    | 语文   | 88    |
| 1002    | 数学   | 90    |
| 1002    | 英语   | 75.5  |
| 1002    | 政治   | 0     |
| 1003    | 语文   | 70    |
| 1003    | 数学   | 85    |
| 1003    | 英语   | 90    |
| 1003    | 政治   | 82    |


{% note info %}

附：UNION 与 UNION ALL 的区别：

1. 对重复结果的处理：UNION 会去掉重复记录，UNION ALL 不会；
2. 对排序的处理：UNION 会排序，UNION ALL 只是简单的将两个结果集合合并；
3. 效率方面的区别：因为 UNION 会做去重和排序，因此效率比 UNION ALL 慢很多。

{% endnote %}

