---
title: "804.唯一的摩尔斯密码词"
description: "804.唯一的摩尔斯密码词"
date: 2020-04-17
updated: 2020-04-17
categories: [LeetCode]
tags: [LeetCode]
top_img:
katex: true
---



## 题目描述



国际摩尔斯密码定义一种标准编码方式，将每个字母对应于一个由一系列点和短线组成的字符串，比如："a" 对应 ".-"，"b" 对应 "-..."，"c" 对应 "-.-." 等等。

为了方便，将所有26个字母对应的摩尔斯密码表如下：

```
[".-","-...","-.-.","-..",".","..-.","--.","....","..",".---","-.-",".-..","--","-.","---",".--.","--.-",".-.","...","-","..-","...-",".--","-..-","-.--","--.."]
```

给定一个单词列表，每个单词可以写成每个字母对应的摩尔斯密码的组合。例如，"cba" 可以写成 "-.-..--..."，（即 "-.-." + ".-" + "-..." 字符串的结合）。我们将这样一个连接过程称作单词翻译。

返回我们可以获得所有不同单词翻译的数量。

**示例 ：**

```
输入：words = ["gin", "zen", "gig", "msg"]
输出：2
解释：各单词翻译如下:
"gin" -> "--...-."
"zen" -> "--...-."
"gig" -> "--...--."
"msg" -> "--...--."

共有 2 种不同翻译, "--...-." 和 "--...--.".
```

**注意：**

1. 每个单词表 $\tt{words}$ 的长度不会超过 $\tt{100}$；
2. 每个单词 $\tt{words[i]}$ 的长度范围为 $\tt{[1, 12]}$；
3. 每个单词 $\tt{words[i]}$ 只包含小写字母。



## 题解


## 方法一：哈希集合

**思路及解法**

我们将数组 $\tt{words}$ 中的每个单词转换为摩尔斯码，并加入哈希集合（HashSet）中，最终的答案即为哈希集合中元素的个数。

**代码**

{% tabs 代码块样式 %}
<!-- tab Java -->


```java
class Solution {
    public int uniqueMorseRepresentations(String[] words) {
        String[] MORSE = new String[]{".-","-...","-.-.","-..",".","..-.","--.",
                         "....","..",".---","-.-",".-..","--","-.",
                         "---",".--.","--.-",".-.","...","-","..-",
                         "...-",".--","-..-","-.--","--.."};

        Set<String> seen = new HashSet();
        for (String word: words) {
            StringBuilder code = new StringBuilder();
            for (char c: word.toCharArray())
                code.append(MORSE[c - 'a']);
            seen.add(code.toString());
        }

        return seen.size();
    }
}
```


<!-- endtab -->
<!-- tab Python -->


```python
class Solution(object):
    def uniqueMorseRepresentations(self, words):
        MORSE = [".-","-...","-.-.","-..",".","..-.","--.",
                 "....","..",".---","-.-",".-..","--","-.",
                 "---",".--.","--.-",".-.","...","-","..-",
                 "...-",".--","-..-","-.--","--.."]

        seen = {"".join(MORSE[ord(c) - ord('a')] for c in word)
                for word in words}

        return len(seen)
```


<!-- endtab -->
{% endtabs %}


**复杂度分析：**

- 时间复杂度：$\rm{O(S)}$，其中 $\tt{S}$ 是数组 $\tt{words}$ 中所有单词的长度之和。
- 空间复杂度：$\rm{O(S)}$。

