---
title: 【题解】 P17080 「Miku × Nene」共鸣序列
published: 2026-07-19
description: 矩阵加速
tags: [题解, 算法]
category: 算法
draft: false
---

### 题意

[题目链接](https://www.luogu.com.cn/problem/P17080)。

给你一个序列 $f$。

$f_i=\begin{cases} a & \text{ if } i=0 \\ b & \text{ if } i=0 \\ p\times f_{i-1}+q\times f_{i-2} & \text{ if } 2\le i\le n\end{cases}$ 

然后输出 $\sum_{0\le i,j,i+j\le n}(p\times f_i)(q\times f_j)$。

### 思路

首先对答案这个式子处理一下。

这个答案发现这个 $pq$ 是可以提到前面的，所以答案是 $pq\sum_{0\le i,j,i+j\le n}f_i\times f_j$。

这个 $i,j$ 有啥性质，就是 $i+j\le n$ 的所有点对，那可以直接把所有 $i+j=k$ 的点对的贡献记录为 $D_k$，然后答案就是 $\sum_{i=0}^{n}D_i$。

考虑 $D_n$ 的值，那么只要 $i$ 固定，又 $i+j=n$ 所以 $j=n-i$，带入 $j=n-i$，所以 $D_n=\sum_{i=0}^{n}f_i\times f_{n-i}$。

对于这个式子来说，突破点在 $f_{n-i}$，这个数在递推中不应该出现，考虑将 $f_{n-i}$ 拆开。

由于 $f_1$ 和 $f_0$ 不能再拆了，所以有一个边界处理，就是 将 $f_{1}\times f_{n-1}$ 和
 $f_n\times f_0$ 扔出来。

所以：$D_n=f_0\times f_n+f_1\times f_{n-1}+\sum_{i=0}^{n-2}f_i\times f_{n-i}$。

因为 $f_0=a,f_1=b$ 所以带入：$D_n=a\times f_n+b\times f_{n-1}+\sum_{i=0}^{n-2}f_i\times f_{n-i}$。

对于 $f_{n-i}$ 拆开！！！拆成 $f_{n-i}=p\times f_{n-i-1}+q\times f_{n-i-2}$。

带入 $D_n=f_0\times f_n+f_1\times f_{n-1}+\sum_{i=0}^{n-2}f_i\times (p\times f_{n-i-1}+q\times f_{n-i-2})$。

括号拆开！！！$D_n=f_0\times f_n+f_1\times f_{n-1}+\sum_{i=0}^{n-2}f_i\times p\times f_{n-i-1}+f_i\times q\times f_{n-i-2}$。

哎，变成两个整体了，右边的有些熟悉，拆开！！！！

$D_n=f_0\times f_n+f_1\times f_{n-1}+\sum_{i=0}^{n-2}f_i\times p\times f_{n-i-1}+\sum_{i=0}^{n-2}f_i\times q\times f_{n-i-2}$。

嗯？右边的不是 $q\times D_{n-2}$ 吗，换元：

$D_n=f_0\times f_n+f_1\times f_{n-1}+q\times D_{n-2}+\sum_{i=0}^{n-2}f_i\times p\times f_{n-i-1}$。

最后就只有一个 $\sum_{i=0}^{n-2}f_i\times p\times f_{n-i-1}$ 比较不正常了，因为 $D_{n-2}$ 得到启示，要把这个东西想方设法转化成 $D_{n-1}$。

观察两个式子：

$D_{n-1}=\sum_{i=0}^{n-1}f_i\times f_{n-1-i}$

$\sum_{i=0}^{n-2} f_{i}f_{n-1-i}$

发现两个式子的区别在于第一个式子中的尾项第二个式子中没有！

那我们在第一个式子中减去第一个式子的尾项 $f_0\times f_{n-1}$ 就是第二个式子。

于是得 $\sum_{i=0}^{n-2} f_{i}f_{n-1-i}=D_{n-1}-f_0\times f_{n-1}$。

又因为 $f_0=a$，所以 $\sum_{i=0}^{n-2} f_{i}f_{n-1-i}=D_{n-1}-a\times f_{n-1}$。

回到原来的式子，将这个 $\sum_{i=0}^{n-2} f_{i}f_{n-1-i}=D_{n-1}-a\times f_{n-1}$ 带入 $D_n=a\times f_n+b\times f_{n-1}+q\times D_{n-2}+p\times\sum_{i=0}^{n-2}f_i\times  f_{n-i-1}$。

所以 $D_n=a\times f_n+b\times f_{n-1}+q\times D_{n-2}+p\times D_{n-1}-p\times a\times f_{n-1}$。

有些混乱，整理一下：

$$D_n=a\times f_n+(b-a\times p)\times f_{n-1}+p\times D_{n-1}+q\times D_{n-2}$$

然后这个 $f_n$ 需要拆开，带入 $f_n=p\times f_{n-1}+q\times f_{n-2}$。

$$D_n=a\times (p\times f_{n-1}+q\times f_{n-2})+(b-a\times p)\times f_{n-1}+p\times D_{n-1}+q\times D_{n-2}$$

去括号：$D_n=a\times p\times f_{n-1}+a\times q\times f_{n-2}+(b-a\times p)\times f_{n-1}+p\times D_{n-1}+q\times D_{n-2}$。

合并同类项：$D_n=(a\times p+b-a\times p)\times f_{n-1}+a\times q\times f_{n-2}+p\times D_{n-1}+q\times D_{n-2}$。

这就是 $D_n$ 的递推式 $D_n=(a\times p+b-a\times p)\times f_{n-1}+a\times q\times f_{n-2}+p\times D_{n-1}+q\times D_{n-2}$。

这个式子一看就十分可以矩阵递推，再加一维统计 $D_i$ 的前缀和，这道题就完成了。

$\left (\begin{bmatrix} p & q & 0 & 0 & 0\\ q & 0 & 0 & 0 & 0\\ b & aq & p & q & 0\\ 0 & 0 & q & 0 & 0\\ b & aq & p & q & 1\end{bmatrix}  \right ) ^{n-1}\times \begin{bmatrix} f_{n-1}\\ f_{n-2}\\ D_{n-1}\\ D_{n-2}\\ s_{n-1}\end{bmatrix}=\begin{bmatrix} f_n\\ f_{n-1} \\ D_n \\ D_{n-1}\\ S_n\end{bmatrix}$

然后卡场吗，这个题的话，取余很慢的，所以可以在矩阵中减少取余操作，在最后统计答案时再取余，但是不要用 __int128，不要怕 long long 爆炸，因为 __int128 的常熟太大会导致特别慢！！！

### 小结

矩阵重点在矩阵吗？在推式子（不是。