---
{"dg-publish":true,"permalink":"//amortized-analysis/"}
---


在讨论类似 cpp 的 vector 之类动态数组（dynamic array）性能 [Dynamic array - Wikipedia](https://en.wikipedia.org/wiki/Dynamic_array#Performance) 的时候，会发现它们有的后面标了一个 "$O(1)$ amortized" 的复杂度。这里 amortized 是摊销或者平摊的意思。有一个术语叫摊还分析。

以 vector 为例，通常添加一个新元素的时间复杂度是 $O(1)$ ，但是当元素数量达到了设定的 capacity $N$ 的时候，就会再次进行扩展（例如变为原来的两倍），而扩展的时间复杂度是 $O(N)$ 级别的。

插入 $N+1$ 个元素，平均插入一个元素需要的时间就是

$$
\frac{N \times O(1)+O(N)}{N+1}
$$

均摊下来还是 $O(1)$ 的。

更多摊还分析的例子可以参考 [Amortized analysis - Wikipedia](https://en.wikipedia.org/wiki/Amortized_analysis)