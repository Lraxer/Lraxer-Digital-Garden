---
{"dg-publish":true,"permalink":"//maxima/"}
---


## Maxima 是什么

maxima 是一款免费开源，基于 #lisp （有多种方言实现）的计算机代数软件，用于多项式化简、分解、积分、微分等符号计算场景。

更多资料可以参考北大数院的入门教程 [Maxima介绍](https://www.math.pku.edu.cn/teachers/lidf/docs/statcomp/html/_statcompbook/maxima.html) 。还有 [PDF 版](https://www.math.pku.edu.cn/teachers/lidf/docs/maxima/maxima-talk.pdf) 。

## 安装

Windows 安装 wxMaxima 。

Archlinux

```shell
sudo pacman -S maxima tk
```

`tk` 目前只能先手动安装，才能用 xmaxima 这个 GUI ，要不只能在命令行里用。

## 一个级数求和的例子

对于级数

$$
\sum_{k=1}^{n} \sum_{j=1}^{k} \sum_{i=1}^j i
$$

简单说就是先对 `[1], [1,2], [1,2,3],[1,2,3,...,k]` 的每一个求和，然后再对这些和求和。把 $k$ 从 $1$ 到 $n$ 的值都重复一遍。

例如 $n=4$ 

```
k=1: (1) = 1
k=2: (1) + (1+2) = 4
k=3: (1) + (1+2) + (1+2+3) = 10
k=4: (1) + (1+2) + (1+2+3) + (1+2+3+4) = 20

sum = 1 + 4 + 10 + 20 = 35
```

下面演示一下通过 maxima 计算等差数列的求和公式，以及这个级数的求和公式。命令行启动 maxima ，输入 `sum(i, i, 1, n),simpsum=true;` （注意一定要结尾带分号）

```
(%i1) sum(i, i, 1, n),simpsum=true;
                                     2
                                    n  + n
(%o1)                               ------
                                      2
(%i2) 
```

直接求上面这个级数的和

```
(%i2) f1: sum(i, i, 1, j);
                                     j
                                    ====
                                    \
(%o2)                                >    i
                                    /
                                    ====
                                    i = 1
(%i3) f2: sum(f1, j, 1, k);
                                  k     j
                                 ====  ====
                                 \     \
(%o3)                             >     >    i
                                 /     /
                                 ====  ====
                                 j = 1 i = 1
(%i4) f3: sum(f2, k, 1, n);
                               n     k     j
                              ====  ====  ====
                              \     \     \
(%o4)                          >     >     >    i
                              /     /     /
                              ====  ====  ====
                              k = 1 j = 1 i = 1
(%i5) f3, simpsum=true;
                    4      3    2      3      2        2
                   n  + 2 n  + n    2 n  + 3 n  + n   n  + n
                   -------------- + --------------- + ------
                         12                6            3
(%o5)              -----------------------------------------
                                       2
(%i6) expand(%);
                               4    3       2
                              n    n    11 n    n
(%o6)                         -- + -- + ----- + -
                              24   4     24     4
(%i7) 
```

可以看到，我们能用 `name: formula` 这种形式创建变量名，用 `%` 代表上一个命令的输出结果。

`expand()` 的作用是多项式合并同类项并化简。