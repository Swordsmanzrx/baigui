---
title: 【总结】 Lagrange 插值法学习笔记
published: 2026-07-14
description: 数学
tags: [总结]
category: 算法
draft: false
---

## 插值

这个东西，要科学的说极为令人看不懂，用人话说就是，给你 $n$ 个点，令你找到一个幂次最小的函数 $f(x)$，使得 $f(x)$ 穿过这 $n$ 个点。

## Lagrange 插值

证明是好的，但是如何想到这个证明是个天才的过程，这里自然不会说也不知道。

### Step one

考虑一个函数 $f'_i(x)$，这个函数穿过了第 $i$ 个点，但这个函数对于所有 $i\ne j$ 在 $x_j$ 上都等于 $0$。

这样的话对于一个点 $(x_i,y_i)$，在 $f'_i(x)$ 上是 $y_i$，在 $f'_j(x)(j\ne i)$ 上是 $0$。

所以 $\sum_{i=1}^{n}f'_i(x_i)$ 的值为 $y_i$，而 $f(x_i)=y_i$。

所以 $f(x_i)=\sum_{i=1}^{n}f'_i(x_i)$。

### Step two

考虑 $f'_i(x)$ 是什么。

就像初中的解一元二次方程一样，解一元二次方程，老师曾经告诉过我一个叫做交点法的东西，就是说但你知道一个二次函数的两个零点在 $x_1,x_2$ 的时候，这个函数就是 $y=a(x-x_1)(x-x_2)$，$a$ 在这里面是一个系数。

这样的话，$x_j(j\ne i)$ 在 $f'_i(x)$ 上是 $0$，可以看作 $x_j$ 是 $f'_i(x)$ 的一个零点，这样的零点，一共有 $n-1$ 个，所以可以将 $f'_i(x)=a\prod_{j\ne i}(x-x_j)$。

然后由于 $f'_i(x)$ 经过 $(x_i,y_i)$，所以带入 $(x_i,y_i)$。

则：

$$y_i=a\prod_{j\ne i}(x_i-x_j)$$

$$a=\frac{y_i}{\prod_{j\ne i}(x_i-x_j)}$$

所以 $a$ 的值就求出来了。

带入 $a$ 的值：

$$f'_i(x)=\frac{y_i}{\prod_{j\ne i}(x_i-x_j)}\times \prod_{j\ne i}(x-x_j)$$

$$f'_i(x)=\frac{y_i\times\prod_{j\ne i}(x-x_j)}{\prod_{j\ne i}(x_i-x_j)}$$

将 $y_i$ 提出来：

$$f'_i(x)=y_i\times\frac{\prod_{j\ne i}(x-x_j)}{\prod_{j\ne i}(x_i-x_j)}$$

发现这两个的 $j$ 取值范围一致，所以：

$$f'_i(x)=y_i\times\prod_{j\ne i}\frac{(x-x_j)}{(x_i-x_j)}$$

### Step three

$$f(x)=\sum_{i=1}^{n}f'_i(x)$$

带入 $f'_i(x)=y_i\times\prod_{j\ne i}\frac{(x-x_j)}{(x_i-x_j)}$：

$$f(x)=\sum_{i=1}^{n}y_i\times\prod_{j\ne i}\frac{(x-x_j)}{(x_i-x_j)}$$

所以现在，你可以使用 $O(n^2)$ 的复杂度将一个多项式求出来了。

### Step four（横坐标是连续整数的 Lagrange 插值）

考虑优化，毕竟可以特殊问题特殊考虑嘛。

考虑横坐标是连续整数。

因为横坐标是连续整数，所以设我们已知 $f(1)\cdots f(n)$。

带入 $(1,f(1)),(2,f(2)),\cdots,(n,f(n))$

得 $f(x)=(\sum_{i=1}^{n}y_i\times \prod_{j\ne i}\frac{x-j}{i-j})$。

对分子分母分开思考。

对于分子 $\prod_{j\ne i}(x-j)$。

可以写成 $\frac{\prod_{1\le j\le n}(x-j)}{x-i}$。

预处理一下 $\prod_{1\le j\le n}(x-j)$ 就可以 $O(1)$ 得分子的结果。

对于分母 $\prod_{j\ne i}(i-j)$，这个可以分成两段。

对于 $1\le j\le i$，就是 $(i-1)\times (i-2)\times \cdots\times(i-(i-1))=(i-1)\times (i-2)\times \cdots\times 1=(i-1)!$。

对于 $i<j\le n$，就是 $(i-(i+1))\times(i-(i+2))\times \cdots\times (i-n)$。

将负号提取一下，得 $(-((i+1)-i))\times(-((i+2)-i))\times \cdots\times (-(n-i))$。

有多少个符号呢，有多少个数字就有多少个符号，一共有 $n-(i+1)+1$ 个数字。

所以最终的符号是 $(-1)^{n-i}$。

所以就是 $(-1)^{(n-i)}\times ((i+1)-i)\times((i+2)-i)\times\cdots\times(n-i)=(-1)^{(n-i)}\times (1)\times(2)\times\cdots\times(n-i)=(-1)^{(n-i)}\times(n-i)!$。

最后的分母是 $(-1)^{n-i}\times (i-1)!\times(n-i)!$。

则 $f(x)=\sum_{i=1}^{n}y_i\times \frac{\frac{\prod_{1\le j\le n}(x-j)}{x-i}}{(-1)^{n-i}\times (i-1)!\times(n-i)!}=\sum_{i=1}^{n}y_i\times \frac{\prod_{1\le j\le n}(x-j)}{(-1)^{n-i}\times (i-1)!\times(n-i)!\times (x-i)}$。

综上所述，对于一个已知 $f(1),f(2),\cdots ,f(n)$ 的多项式函数 $f(x)$，有 $f(x)=\sum_{i=1}^{n}y_i\times \frac{\prod_{1\le j\le n}(x-j)}{(-1)^{n-i}\times (i-1)!\times(n-i)!\times (x-i)}$。

而阶乘和逆元是可以预处理的，如果已经知道了 $x$，那分子也是可以预处理的，最后的复杂度为 $O(n)$。

参考资料：[OI-Wiki](https://oi-wiki.org/math/numerical/interp/#%E6%A8%AA%E5%9D%90%E6%A0%87%E6%98%AF%E8%BF%9E%E7%BB%AD%E6%95%B4%E6%95%B0%E7%9A%84-lagrange-%E6%8F%92%E5%80%BC)。