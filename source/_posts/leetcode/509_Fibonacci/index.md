---
title: "509.斐波那契数"
description: "509.斐波那契数"
date: 2020-04-14
updated: 2020-04-14
categories: [LeetCode]
tags: [LeetCode]
top_img:
katex: true
---


## 描述

斐波那契数，通常用 $\tt{F(n)}$ 表示，形成的序列称为 斐波那契数列 。该数列由 `0` 和 `1` 开始，后面的每一项数字都是前面两项数字的和。也就是：

```
F(0) = 0，F(1) = 1
F(n) = F(n - 1) + F(n - 2)，其中 n > 1
```

给你 $\tt{n}$，请计算 $\tt{F(n)}$。

**示例 1：**

```
输入：2
输出：1
解释：F(2) = F(1) + F(0) = 1 + 0 = 1
```

**示例 2：**

```
输入：3
输出：2
解释：F(3) = F(2) + F(1) = 1 + 1 = 2
```

**示例 3：**

```
输入：4
输出：3
解释：F(4) = F(3) + F(2) = 2 + 1 = 3
```

**提示：**

- $\tt{0 \leq n \leq 30}$



## 方法一：动态规划

斐波那契数的边界条件是 $\tt{F(0)=0}$ 和 $\tt{F(1)=1}$。当 $\tt{n > 1}$ 时，每一项的和都等于前两项的和，因此有如下递推关系：

$$\tt{F(n) = F(n-1) + F(n-2)}$$

由于斐波那契数存在递推关系，因此可以使用动态规划求解。动态规划的状态转移方程即为上述递推关系，边界条件为 $\tt{F(0)}$ 和 $\tt{F(1)}$。

根据状态转移方程和边界条件，可以得到时间复杂度和空间复杂度都是 $\rm{O(n)}$ 的实现。由于 $\tt{F(n)}$ 只和 $\tt{F(n-1)}$ 与 $\tt{F(n-2)}$ 有关，因此可以使用「滚动数组思想」把空间复杂度优化成 $\rm{O(1)}$。**如下的代码中给出的就是这种实现。**

{% tabs 代码块样式 %}
<!-- tab Java -->


```java
class Solution {
    public int fib(int n) {
        if (n < 2) {
            return n;
        }
        // 递归方式
        // return fib(n - 1) + fib(n - 2);
        
        // 动态规划（滚动数组）
        // 斐波那契数列每一项是前两项的和
        // 设置三个变量，r 表示当前项，q 和 p 表示前两项
        // 注意当前项从斐波那契数列的第三项开始，前两项值分别是 0 和 1，第三项的值则也是 1
        int p = 0, q = 0, r = 1;
        for (int i = 2; i <= n; ++i) {
            // 数组向前滚动
            // 第二项变成第一项
            // 第三项变成第二项
            // 新的目标项也是第三项，即为前两项之和
            p = q;
            q = r;
            r = p + q;
        }
        return r;
    }
}
```


<!-- endtab -->
<!-- tab Python -->


```python
class Solution:
    def fib(self, n: int) -> int:
        if n < 2:
            return n
        
        p, q, r = 0, 0, 1
        for i in range(2, n + 1):
            p, q = q, r
            r = p + q
        
        return r
```


<!-- endtab -->
<!-- tab C -->


```c
int fib(int n) {
    if (n < 2) {
        return n;
    }
    int p = 0, q = 0, r = 1;
    for (int i = 2; i <= n; ++i) {
        p = q;
        q = r;
        r = p + q;
    }
    return r;
}
```


<!-- endtab -->
<!-- tab JavaScript -->


```javascript
var fib = function(n) {
    if (n < 2) {
        return n;
    }
    let p = 0, q = 0, r = 1;
    for (let i = 2; i <= n; i++) {
        p = q;
        q = r;
        r = p + q;
    }
    return r;
};
```


<!-- endtab -->
{% endtabs %}


## 方法二：矩阵快速幂

方法一的时间复杂度是 $\rm{O(n)}$。使用矩阵快速幂的方法可以降低时间复杂度。

首先我们可以构建这样一个递推关系：

$$\left [ \begin{matrix} 1 & 1 \\\\ 1 & 0 \end{matrix} \right] \left [ \begin{matrix} \tt{F(n)} \\\\ \tt{F(n-1)} \end{matrix} \right] = \left [ \begin{matrix} \tt{F(n) + F(n-1)} \\\\ \tt{F(n)} \end{matrix} \right] = \left [ \begin{matrix} \tt{F(n+1)} \\\\ \tt{F(n)} \end{matrix} \right]$$

因此：

$$\left [ \begin{matrix} \tt{F(n+1)} \\\\ \tt{F(n)} \end{matrix} \right] = \left [ \begin{matrix} 1 & 1 \\\\ 1 & 0 \end{matrix} \right] ^n \left [ \begin{matrix} \tt{F(1)} \\\\ \tt{F(0)} \end{matrix} \right]$$

令：

$$M = \left [ \begin{matrix} 1 & 1 \\\\ 1 & 0 \end{matrix} \right]$$

因此只要我们能快速计算矩阵 $M$ 的 $\tt{n}$ 次幂，就可以得到 $\tt{F(n)}$ 的值。如果直接求取 $M^n$，时间复杂度是 $\rm{O(n)}$，可以定义矩阵乘法，然后用快速幂算法来加速这里 $M^\tt{n}$ 的求取。

{% tabs 代码块样式 %}
<!-- tab Java -->


```java
class Solution {
    public int fib(int n) {
        if (n < 2) {
            return n;
        }
        int[][] q = {{1, 1}, {1, 0}};
        int[][] res = pow(q, n - 1);
        return res[0][0];
    }

    public int[][] pow(int[][] a, int n) {
        int[][] ret = {{1, 0}, {0, 1}};
        while (n > 0) {
            if ((n & 1) == 1) {
                ret = multiply(ret, a);
            }
            n >>= 1;
            a = multiply(a, a);
        }
        return ret;
    }

    public int[][] multiply(int[][] a, int[][] b) {
        int[][] c = new int[2][2];
        for (int i = 0; i < 2; i++) {
            for (int j = 0; j < 2; j++) {
                c[i][j] = a[i][0] * b[0][j] + a[i][1] * b[1][j];
            }
        }
        return c;
    }
}
```


<!-- endtab -->
<!-- tab Python -->


```python
class Solution:
    def fib(self, n: int) -> int:
        if n < 2:
            return n
        
        q = [[1, 1], [1, 0]]
        res = self.matrix_pow(q, n - 1)
        return res[0][0]
    
    def matrix_pow(self, a: List[List[int]], n: int) -> List[List[int]]:
        ret = [[1, 0], [0, 1]]
        while n > 0:
            if n & 1:
                ret = self.matrix_multiply(ret, a)
            n >>= 1
            a = self.matrix_multiply(a, a)
        return ret

    def matrix_multiply(self, a: List[List[int]], b: List[List[int]]) -> List[List[int]]:
        c = [[0, 0], [0, 0]]
        for i in range(2):
            for j in range(2):
                c[i][j] = a[i][0] * b[0][j] + a[i][1] * b[1][j]
        return c
```


<!-- endtab -->
<!-- tab C -->


```c
struct Matrix {
    int mat[2][2];
};

struct Matrix matrixMultiply(struct Matrix* a, struct Matrix* b) {
    struct Matrix c;
    for (int i = 0; i < 2; i++) {
        for (int j = 0; j < 2; j++) {
            c.mat[i][j] = (*a).mat[i][0] * (*b).mat[0][j] + (*a).mat[i][1] * (*b).mat[1][j];
        }
    }
    return c;
}

struct Matrix matrixPow(struct Matrix a, int n) {
    struct Matrix ret;
    ret.mat[0][0] = ret.mat[1][1] = 1;
    ret.mat[0][1] = ret.mat[1][0] = 0;
    while (n > 0) {
        if (n & 1) {
            ret = matrixMultiply(&ret, &a);
        }
        n >>= 1;
        a = matrixMultiply(&a, &a);
    }
    return ret;
}

int fib(int n) {
    if (n < 2) {
        return n;
    }
    struct Matrix q;
    q.mat[0][0] = q.mat[0][1] = q.mat[1][0] = 1;
    q.mat[1][1] = 0;
    struct Matrix res = matrixPow(q, n - 1);
    return res.mat[0][0];
}
```


<!-- endtab -->
<!-- tab JavaScript -->


```javascript
var fib = function(n) {
    if (n < 2) {
        return n;
    }
    const q = [[1, 1], [1, 0]];
    const res = pow(q, n - 1);
    return res[0][0];
};

const pow = (a, n) => {
    let ret = [[1, 0], [0, 1]];
    while (n > 0) {
        if ((n & 1) === 1) {
            ret = multiply(ret, a);
        }
        n >>= 1;
        a = multiply(a, a);
    }
    return ret;
}

const multiply = (a, b) => {
    const c = new Array(2).fill(0).map(() => new Array(2).fill(0));
    for (let i = 0; i < 2; i++) {
        for (let j = 0; j < 2; j++) {
            c[i][j] = a[i][0] * b[0][j] + a[i][1] * b[1][j];
        }
    }
    return c;
}
```


<!-- endtab -->
{% endtabs %}



## 方法三：通项公式

斐波那契数 $\tt{F(n)}$ 是齐次线性递推，根据递推方程 $\tt{F(n) = F(n-1) + F(n-2)}$

可以写出这样的特征方程：$\tt{x^2 = x + 1}$

求得 $\tt{x_1 = \frac{ 1 + \sqrt{5} }{2}}$，$\tt{x_2 = \frac{ 1 - \sqrt{5} }{2}}$。

设通解为 $\tt{F(n) = c_1x_1^n} + c_2x_2^n$

代入初始条件 $\tt{F(0) = 0}$，$\tt{F(1) = 1}$

得 $\tt{c_1 = \frac{1}{\sqrt{5}}}$，$\tt{c_1 = - \frac{1}{\sqrt{5}}}$

因此斐波那契数的通项公式如下：

$$\tt{F(n)} = \frac{1}{\sqrt{5}} \Bigg[ \Bigg( \frac{ 1 + \sqrt{5} }{2} \Bigg)^n - \Bigg( \frac{ 1 - \sqrt{5} }{2}  \Bigg)^n \Bigg]$$

得到通项公式之后，就可以通过公式直接求解第 $\tt{n}$ 项。

{% tabs 代码块样式 %}
<!-- tab Java -->


```java
class Solution {
    public int fib(int n) {
        double sqrt5 = Math.sqrt(5);
        double fibN = Math.pow((1 + sqrt5) / 2, n) - Math.pow((1 - sqrt5) / 2, n);
        return (int) Math.round(fibN / sqrt5);
    }
}
```


<!-- endtab -->
<!-- tab Python -->


```python
class Solution:
    def fib(self, n: int) -> int:
        sqrt5 = 5**0.5
        fibN = ((1 + sqrt5) / 2) ** n - ((1 - sqrt5) / 2) ** n
        return round(fibN / sqrt5)
```


<!-- endtab -->
<!-- tab C -->


```c
int fib(int n) {
    double sqrt5 = sqrt(5);
    double fibN = pow((1 + sqrt5) / 2, n) - pow((1 - sqrt5) / 2, n);
    return round(fibN / sqrt5);
}
```


<!-- endtab -->
<!-- tab JavaScript -->


```javascript
var fib = function(n) {
    const sqrt5 = Math.sqrt(5);
    const fibN = Math.pow((1 + sqrt5) / 2, n) - Math.pow((1 - sqrt5) / 2, n);
    return Math.round(fibN / sqrt5);
};
```


<!-- endtab -->
{% endtabs %}



