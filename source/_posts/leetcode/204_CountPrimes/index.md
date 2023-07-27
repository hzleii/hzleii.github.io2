---
title: "204.计算质数"
description: "204.计算质数"
date: 2020-04-10
updated: 2020-04-10
categories: [LeetCode]
tags: [LeetCode]
top_img:
katex: true
---



## 题目描述

**统计所有小于非负整数 $\tt{n}$ 的质数的数量**

**示例 1：**

```
输入：n = 10
输出：4
解释：小于 10 的质数一共有4个，它们是 2，3，5，7
```

**示例 2：**

```
输入：n = 0
输出：0
```

**示例 3：**

```
输入：n = 1
输出：0
```

**提示：**

$\tt{0 \leq n \leq 5 \times 10^6}$



## 题解

统计 $\tt{[2, n]}$ 中质数的数量是一个很常见的题目，也有很多巧妙高效的做法，接下来的部分只会讲述一些常见的做法，更多的拓展内容读者可以自行搜索补充，也欢迎在评论区与大家分享交流。



## 方法一：枚举

很直观的思路是我们枚举每个数判断其是不是质数。

考虑质数的定义：在大于 tt{1}$ 的自然数中，除了 $\tt{1}$ 和它本身以外不再有其他因数的自然数。因此对于每个数 $\tt{x}$，我们可以从小到大枚举 $\tt{[2, x -1]}$ 中的每个数 ，判断 $\tt{y}$ 是否为 $\tt{x}$ 的因数。但这样判断一个数是否为质数的时间复杂度最差情况下会到 $\rm{O(n)}$，无法通过所有的测试数据。

考虑到如果 $\tt{y}$ 是 $\tt{x}$ 的因数，那么 $\tt{\frac{x}{y}}$ 也必然是 $\tt{x}$ 的因数，因此我们只要校验 $\tt{y}$ 或者 $\tt{\frac{x}{y}}$ 即可。而如果我们每次选择校验两者中的较小数，则不难发现较小数一定落在 $\tt{[2, \sqrt{x}]}$ 的区间中，因此我们只要枚举 $\tt{[2, \sqrt{x}]}$ 中的所有数即可，这样单次检查的时间复杂度从 $\rm{O(n)}$ 降低至了 $\rm{O(\sqrt{x})}$。



**代码展示：**

{% tabs 代码块样式 %}
<!-- tab Java -->


```java
class Solution {
    public int countPrimes(int n) {
        int ans = 0;
        for (int i = 2; i < n; ++i) {
            ans += isPrime(i) ? 1 : 0;
        }
        return ans;
    }

    public boolean isPrime(int x) {
        for (int i = 2; i * i <= x; ++i) {
            if (x % i == 0) {
                return false;
            }
        }
        return true;
    }
}
```


<!-- endtab -->
<!-- tab C -->


```c
bool isPrime(int x) {
    for (int i = 2; i * i <= x; ++i) {
        if (x % i == 0) {
            return false;
        }
    }
    return true;
}

int countPrimes(int n) {
    int ans = 0;
    for (int i = 2; i < n; ++i) {
        ans += isPrime(i);
    }
    return ans;
}
```


<!-- endtab -->
<!-- tab JavaScript -->


```javascript
const isPrime = (x) => {
    for (let i = 2; i * i <= x; ++i) {
        if (x % i == 0) {
            return false;
        }
    }
    return true;
}

var countPrimes = function(n) {
    let ans = 0;
    for (let i = 2; i < n; ++i) {
        ans += isPrime(i);
    }
    return ans;
};
```

<!-- endtab -->
{% endtabs %}


**复杂度分析：**

- 时间复杂度 $\rm{O(n\sqrt{n})}$。单个数检查的时间复杂度为 $\rm{O(\sqrt{n})}$，一共要检查 $\rm{O(\sqrt{n})}$ 个数，因此总时间复杂为 $\rm{O(n\sqrt{n})}$。
- 空间复杂度 $\rm{O(1)}$。



## 方法二：埃氏筛

枚举没有考虑到数与数的关联性，因此难以再继续优化时间复杂度。接下来我们介绍一个常见的算法，该算法由希腊数学家厄拉多塞 $(\tt EratosthenesEratosthenes)$ 提出，称为厄拉多塞筛法，简称埃氏筛。

我们考虑这样一个事实：如果 $\tt{x}$ 是质数，那么大于 $\tt{x}$ 的 $\tt{x}$ 的倍数 $\tt{2x, 3x,\ldots}$ 一定不是质数，因此我们可以从这里入手。

我们设 $\tt{isPrime[i]}$ 表示数 $\tt{i}$ 是不是质数，如果是质数则为 $\tt{x}$，否则为 $\tt{0}$。从小到大遍历每个数，如果这个数为质数，则将其所有的倍数都标记为合数（除了该质数本身），即$\tt{0}$，这样在运行结束的时候我们即能知道质数的个数。

这种方法的正确性是比较显然的：这种方法显然不会将质数标记成合数；另一方面，当从小到大遍历到数 $\tt{x}$ 时，倘若它是合数，则它一定是某个小于 $\tt{x}$ 的质数 $\tt{y}$ 的整数倍，故根据此方法的步骤，我们在遍历到 $\tt{y}$ 时，就一定会在此时将 $\tt{x}$ 标记为 $\tt{isPrime[x]=0}$。因此，这种方法也不会将合数标记为质数。

当然这里还可以继续优化，对于一个质数 $\tt{x}$，如果按上文说的我们从 $\tt{2x}$ 开始标记其实是冗余的，应该直接从 $\tt{x \cdot x}$ 开始标记，因为 $\tt{2x,3x,\ldots}$ 这些数一定在 $\tt{x}$ 之前就被其他数的倍数标记过了，例如 $\tt{2}$ 的所有倍数，$\tt{3}$ 的所有倍数等。



**代码展示：**

{% tabs 代码块样式 %}
<!-- tab Java -->

```java
class Solution {
    public int countPrimes(int n) {
        int[] isPrime = new int[n];
        Arrays.fill(isPrime, 1);
        int ans = 0;
        for (int i = 2; i < n; ++i) {
            if (isPrime[i] == 1) {
                ans += 1;
                if ((long) i * i < n) {
                    for (int j = i * i; j < n; j += i) {
                        isPrime[j] = 0;
                    }
                }
            }
        }
        return ans;
    }
}
```


<!-- endtab -->
<!-- tab C -->


```c
int countPrimes(int n) {
    if (n < 2) {
        return 0;
    }
    int isPrime[n];
    memset(isPrime, 0, sizeof(isPrime));
    int ans = 0;
    for (int i = 2; i < n; ++i) {
        if (!isPrime[i]) {
            ans += 1;
            if ((long long)i * i < n) {
                for (int j = i * i; j < n; j += i) {
                    isPrime[j] = 1;
                }
            }
        }
    }
    return ans;
}
```


<!-- endtab -->
<!-- tab JavaScript -->


```javascript
var countPrimes = function(n) {
    const isPrime = new Array(n).fill(1);
    let ans = 0;
    for (let i = 2; i < n; ++i) {
        if (isPrime[i]) {
            ans += 1;
            for (let j = i * i; j < n; j += i) {
                isPrime[j] = 0;
            }
        }
    }
    return ans;
};
```

<!-- endtab -->
{% endtabs %}


**复杂度 分析：**

- 时间复杂度：$\rm{O(n \log \log n)}$。
- 空间复杂度：$\rm{O(n)}$。我们需要 $\rm{O(n)}$ 的空间记录每个数是否为质数。