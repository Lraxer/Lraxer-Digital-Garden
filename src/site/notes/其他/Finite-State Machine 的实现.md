---
{"dg-publish":true,"permalink":"/其他/Finite-State Machine 的实现/","tags":["algorithm"]}
---


## 有限状态机

参考 [有限状态机 - Wikipedia](https://zh.wikipedia.org/wiki/%E6%9C%89%E9%99%90%E7%8A%B6%E6%80%81%E6%9C%BA) ：

> 有限状态机（英语：finite-state machine，缩写：FSM）又称有限状态自动机（英语：finite-state automaton，缩写：FSA），简称状态机，是表示有限个状态以及在这些状态之间的转移和动作等行为的数学计算模型。  

## 例题

以 [剑指 Offer 20. 表示数值的字符串 - 力扣（LeetCode）](https://leetcode.cn/problems/biao-shi-shu-zhi-de-zi-fu-chuan-lcof/) 为例。

### 题目描述

请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。

数值（按顺序）可以分成以下几个部分：

1. 若干空格
2. 一个小数或者整数
3. （可选）一个 'e' 或 'E' ，后面跟着一个整数
4. 若干空格

小数（按顺序）可以分成以下几个部分：

1. （可选）一个符号字符（'+' 或 '-'）
2. 下述格式之一：
	1. 至少一位数字，后面跟着一个点 '.'
	2. 至少一位数字，后面跟着一个点 '.' ，后面再跟着至少一位数字
	3. 一个点 '.' ，后面跟着至少一位数字

整数（按顺序）可以分成以下几个部分：

1. （可选）一个符号字符（'+' 或 '-'）
2. 至少一位数字

### 示例

部分数值列举如下：

```
["+100", "5e2", "-123", "3.1416", "-1E-16", "0123"]
```

部分非数值列举如下：

```
["12e", "1a3.14", "1.2.3", "+-5", "12e+5.4"]
```

示例 1：

```
输入：s = "0"
输出：true
```

示例 2：

```
输入：s = "e"
输出：false
```

示例 3：

```
输入：s = "."
输出：false
```

示例 4：

```
输入：s = "    .1  "
输出：true
```

## 解答

这一类问题的约束条件往往非常复杂，很难用简单的分支处理，尽管这道题的要求还不算非常复杂。

有限状态机的方式可以提供一种解决这类问题的通解。

### 状态转移图

第一步是根据题目描述，确定状态和转移条件，画出状态转移的图像。这一步很关键，也是最容易出问题的。例如本题可以画出下面的图像。（这个状态转移图使用 [[其他/Graphviz\|Graphviz]] 绘制）

![Pasted image 20230103000000.png](/img/user/00%20Attachments/Pasted%20image%2020230103000000.png)

其中状态 0 是起始状态，2、4、7、8 是合法的终止状态。

### 实现

对每一个状态，可以使用一个哈希表保存它的状态转移途径。例如本题中，哈希表的 key 代表了字符串中当前位置的字符是空格、符号、小数点、科学计数法的 e 或者数字，分别用字符 `s, i, d, e, n` 表示。哈希表的 value 是转移到的下一个状态。我们遍历给定的字符串，根据当前位置的字符确定 key，如果这个 key 在当前状态的哈希表里找不到，就说明给定的字符串非法；如果能找到，就转移到下一个状态。如果最后的状态是 2、4、7、8 中的任意一个，才是合法的字符串。

```cpp
class Solution {
public:
  bool isNumber(string s) {
    vector<unordered_map<char, int>> map_vec{
        { {'s', 0}, {'i', 1}, {'d', 3}, {'n', 2} },
        { {'d', 3}, {'n', 2} },
        { {'n', 2}, {'d', 4}, {'e', 5}, {'s', 8} },
        { {'n', 4} },
        { {'n', 4}, {'e', 5}, {'s', 8} },
        { {'i', 6}, {'n', 7} },
        { {'n', 7}},
        { {'n', 7}, {'s', 8} },
        { {'s', 8} } };

    int state = 0;
    char t;
    for (auto &c : s) {
      if (c >= '0' && c <= '9') {
        t = 'n';
      } else {
        switch (c) {
        case ' ':
          t = 's';
          break;
        case '.':
          t = 'd';
          break;
        case '+':
        case '-':
          t = 'i';
          break;
        case 'e':
        case 'E':
          t = 'e';
          break;
        default:
          t = '?';
          break;
        }
      }
      auto new_state = map_vec[state].find(t);
      if (new_state != map_vec[state].end()) {
        state = new_state->second;
      } else {
        return false;
      }
    }
    return (state == 2) || (state == 4) || (state == 7) || (state == 8);
  }
};
```
