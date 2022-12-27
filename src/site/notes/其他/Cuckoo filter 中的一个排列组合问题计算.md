---
{"dg-publish":true,"permalink":"/其他/Cuckoo filter 中的一个排列组合问题计算/"}
---


## Cuckoo filter 论文里面提到的优化方法

一个 bucket 有 4 个 slot ，每个 slot 记录一个 4-bit 指纹。易得这个 bucket 需要的空间是 16 bits 。注意，这里指纹是可以重复的。关于指纹重复的问题在 3.1 最后有提到，解决方法可以有很多种，但是指纹重复在有些情况下是允许的。

压缩方式：将这个 bucket 存放的 4 个指纹排序（这里假设从小到大）。一共有 **3876** 种可能的组合。 3876 用 12 位二进制就可以表示，将 3876 种可能提前存入一个表中，就可以用 12-bit 的索引代替 16-bit 的指纹长度了，相当于每个指纹节省了 1 bit 的空间。

## 排序后组合数量的计算

下面分析一下这个 3876 是怎么算出来的。 4-bit 长度的指纹一共有 16 种：

```
1:  0000
2:  0001
3:  0010
...
16: 1111
```

由于指纹可以重复，所以相邻的两个指纹可以是相等的，但是后面的指纹一定大于等于前面的指纹。

下面用索引 `1` 代替指纹 `0000` ， `2` 代替 `0001` ，等等，假设前三个指纹是 `1 1 1` ，那么一共有 16 种情况，即第四个指纹是 `1-16` 中的一个；假设前三个指纹是 `1 1 2` ，则共有 15 种情况。以此类推，因此前两个指纹是 `1 1` 时，共有 $\sum^{16}_{i=1}i$ 种情况。

对于前两个指纹 `1 2` ，一共有 $\sum^{15}_{i=1}i$ 种情况。这样类推下去，第一个指纹为 `1` 时，一共有 $\sum^{16}_{j=1} \sum^j_{i=1}i$ 种情况。

那么第一个指纹从 `1` 到 `16` ，共有

$$
\sum^{16}_{k=1}\sum^{k}_{j=1}\sum^j_{i=1}i
$$

种情况。

下面的代码保留了所有中间值。

```python
import math

# 等差数列 1 ~ num 求和
def sum1(num: int):
    if(num<=1):
        return 1
    return (num*(1+num))/2

def compute():
    sumnum = list()
    totalsum = list()
    for i in range(1, 17):
        sumnum.append(sum1(i))
    
    for j in range(1, 17):
        totalsum.append(sum(sumnum[:(17-j)]))
    
    return sum(totalsum)
    
if __name__=="__main__":
    retval = compute()
    print(retval)
```

下面的代码完全按照推导的公式计算。但是很慢。

```python
import math

def compute():
    c = 0
    for k in range(1,17):
        for j in range(1,k+1):
            for i in range(1, j+1):
                c+=i
    
    print(c)

    
if __name__=="__main__":
    compute()
```

## 公式的推广

上面的公式计算的只是一个 bucket 有 4 个指纹（称为 fingerprint 或 tag），每个指纹的长度是 4-bit 的情况。首先要明确的是，这个优化算法 **只针对一个 bucket 有 4 个指纹的情况** 。

在原论文中作者提到：

> Also, when fingerprints are larger than four bits, only the four most significant bits of each fingerprint are encoded; the remainder are stored directly and separately.

所以对其他的指纹长度，一个 bucket 节省的空间也是 4-bit 。

---

不过就算直接建表，那么对于其他的指纹长度，实际上一个 bucket 节省的空间也是 4-bit ，作者的做法表项更少，比直接建表效果更好。下面从数学上分析一下直接建表为什么也是节省 4-bit 。

在数学上，我们想要证明的就是下面的公式：

$$
\left\lceil \log_2 \left( \sum_{k=1}^{2^{fp}} \sum_{j=1}^{k} \sum_{i=1}^j i \right) \right\rceil = 4 \times fp - 4 \\
$$

其中 $fp$ 代表一个指纹的比特数。

通过 [[其他/Maxima 简介\|maxima]] 可以求出里面这个级数的值。求法可以参考 [[其他/Maxima 简介#一个级数求和的例子\|Maxima 简介#一个级数求和的例子]] 。总之这个级数的和可以表示为：

$$
\mathrm{sum}\left( \sum_{k=1}^{n} \sum_{j=1}^{k} \sum_{i=1}^j i \right) = \frac{n^4}{24}+\frac{n^3}{4}+\frac{11n^2}{24}+\frac{n}{4}
$$

我们只关心最高次项，因为它决定了二进制数的长度。因此证明上面的公式等价于证明：

$$
\left\lceil \log_2 \frac{2^{4fp}}{24} \right\rceil = 4 \times fp - 4
$$

化简一下可得

$$
\begin{align}
	\left\lceil 4 \times fp - 2 - \log_2 6 \right\rceil = 4 \times fp - 4 \\
	4 \times fp - 5 < 4 \times fp - 2 - \log_2 6 \le 4 \times fp - 4 \\
	2 \le \log_2 6 < 3
\end{align}
$$

很显然 $\log_2 6$ 在这个范围里，得证。