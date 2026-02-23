---
title: TBB pro preface
date: 2023-03-22 15:50:43
mathjax: true
tags: 
- TBB
- 并行编程
category: 
- TBB PRO
---



这个系列是TBB pro的读书笔记。这是第一篇，preface部分。

perface叙述了很多并行编程相关的知识，包括背景和动机，分类、评价指标和影响性能的因素等。这里记录部分内容。

<!--more-->


## Amdahl定律

定律指出了对一个任务最多可以加速多少。定义了最大的加速比为：

\begin{equation}
S=\frac{1}{(1-a)+\frac{a}{k}}
\end{equation}

其中a为可以加速部分的比例，k为核心数。通过阿姆达尔定律可以得出一个结论，增加核心数所带来的收益逐渐下降，并收敛于$S = \frac{1}{1-a}$.

## Gustafson定律

\begin{equation}
S = \frac{t_{seq} + p \times t_{par}}{t_{seq} + t_{par}}
\end{equation}

其中t_{seq} 为串行部分比例，t_{par}为并行部分比例。区别于Amdahl定律固定问题规模，Gustafson定律认为在增加并行度时，总希望处理跟多的数据。

## 数据局部性影响

我们知道现代计算机采用了多层存储结构，Cache的访问速度要比DRAM内存快得多，因此应尽量提高Cache的命中率。以矩阵乘法为了

```c++
for(int i = 0; i < N; i++) 
    for(int j = 0; j < N; j++) 
        for(int k = 0; k < N; k++)
            c[i][j] += a[i][k] * b[k][j]
```

在这个实现方式中，a矩阵是按行主序，b矩阵是按列主序。这个执行时间为19+mins。

```c++
for(int i = 0; i < N; i++) 
    for(int k = 0; k < N; k++) 
        for(int j = 0; j < N; j++)
            c[i][j] += a[i][k] * b[k][j]
```

第二个实现方式只是将j与k的迭代交换了顺序，但带来大量巨大的加速比，执行时间为4+mins，是第一种的4倍多。原因是第二种方式中，b矩阵是按行访问的，在按行主序存储的矩阵中会有更高的cache命中率。

还有一些其它的方式可以提高数据局部性，比如Z遍历等。

**然而，利用perf测得第一中的cache miss rate大约为20%，而第二种居然为98%。这是非常奇怪的！！！！**不知道是不是perf测量的数据并不可靠。



preface还谈了其它的内容，比如伪共享、SIMD等，但只是一笔带过，只能知道个名字而已。







