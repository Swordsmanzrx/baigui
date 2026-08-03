---
title: 【总结】ST 表
published: 2025-08-07
description: 算法
tags: [总结]
category: 算法
draft: false
---

## $ST$ 表

$ST$ 表这个东西也是十分的 $easy$ 了。

这里给道板子。

[$link$](www.luogu.com.cn/problem/P3865)。

怎末说 $ST$ 表这个东西呢，其实就是一个简单 $dp$。

就是一个 $f_{i,j}$ 表示从 $i$ 开始往后 $2^j$ 个数字中的最大或最小值。

转移也很好转：$f_{i,j}=max/min(f_{i,j-1},f_{i+2^{j-1}-1,j-1})$。

很明了吧。

最后的查询呢？

更简单，查询 $[l,r]$ 中的最大值为例吧。

现设 $x=\left \lfloor log_{r-l+1} \right \rfloor $。

$Max_{l,r}=max{f_{l,x},f_{r-2^x,r}}$。

可见这两个区间有可能有交集，不过这又怎样呢，只要求出最大值即可。

```
void st(){
	for(ll i=1;i<=n;i++)f[i][0]=a[i];
	for(ll j=1;j<=18;j++){
		for(ll i=1;i<=n-(1<<j)+1;i++){
			f[i][j]=max(f[i][j-1],f[i+(1<<(j-1))][j-1]);
		}
	}
}
ll query(ll l,ll r){
	ll g=lg[r-l+1];
	return max(f[l][g],f[r-   t[g] +1  ][g]);
}
```