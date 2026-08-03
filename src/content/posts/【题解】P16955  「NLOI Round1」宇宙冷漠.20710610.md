---
title: 【题解】宇宙冷漠
published: 2026-06-22
description: 题解
tags: [题解]
category: 算法
draft: false
---

为啥要写线段树啊。

这个题整体的思路不就是一个类似并查集的过程吗。

首先找到第 $i$ 个位置的父亲 $j$，使得 $j$ 是最小的且使第 $j$ 个函数到第 $j$ 个函数都共交于一点。

这样的话，看 $l$ 和 $r$ 是否是属于一个父亲的就行了。

为什么？

因为，如果第 $i$ 个函数与第 $i-1$ 函数和第 $i-2$ 个函数交于一点，则 $i-1$ 就满足成为 $fa_i$ 的条件，则 $fa_{i-1}$ 也满足成为 $fa_i$ 的条件，所以 $fa_i$ 是 $fa_{i-1}$。

如果不交于一点，则 $fa_i$ 就是 $i$。

若 $l$ 到 $r$ 都共交于一点，则父亲都应是相等的，否则就说明中间有函数断开了。

注意的是，要判断 $fa_{l+1}$ 是否与 $fa_r$ 一致。

因为 $fa_l$ 代表的是 $l-1$ 和 $l$。

但我们只需关注 $l$。

所以必要从 $l$ 的后头 $l-1$ 检查。

不过，两个函数完全重合的情况需考虑，但我们考虑的只是连续的函数，所以可以像离散化一样将连续的重合的函数整合成一个。

平行也是个问题，因为你求交点时需要除斜率差，平行则说明斜率差为 $0$，所以需要特判。

如果 $i$ 与 $i-1$ 是平行的，则 $i$ 与 $i-1$ 无交点，所以 $fa_i=i$。

若 $i-1$ 与 $i-2$ 是平行的，且经过上面的操作 $i$ 与 $i-1$ 不平行也不重合，则想想 $i$ 与 $i-1$ 必有交点，把 $i$ 的父亲设为 $fa_{i-1}$ 即 $i-1$。

```
#include<bits/stdc++.h>
using namespace std;
typedef int ll;
typedef __int128_t i128;
const ll N=1e6+5;
ll n,q;
struct nid {
	ll k,b;
} h[N],u[N];
ll p[N];
ll id[N];
ll idt[N];
int main() {
    ios::sync_with_stdio(0);
    cin.tie(0),cout.tie(0);
	cin>>n>>q;
	ll tot=0;
	for(ll i=1; i<=n; i++) {
		cin>>h[i].k>>h[i].b;
		if(i==1||u[tot].k!=h[i].k||u[tot].b!=h[i].b)u[++tot]=h[i],id[i]=tot;
		else id[i]=tot;
	}
	idt[2]=idt[1]=1;
	for(int i = 3; i <= tot; ++i) {
		if(u[i-1].k==u[i].k){
			idt[i]=i;
			continue;
		}
		if(u[i-1].k==u[i-2].k){
			idt[i]=i-1;
			continue;
		}
	    long long lhs = (long long)(u[i-1].b - u[i-2].b) * (u[i-1].k - u[i].k);
		long long rhs = (long long)(u[i].b - u[i-1].b) * (u[i-2].k - u[i-1].k);
		if(lhs == rhs) idt[i] = idt[i-1];
		else idt[i] = i;
	}
	while(q--) {
		ll l,r;
		cin>>l>>r;
		if(l==r||id[l]==id[r])cout<<"Yes\n";
		else if(idt[id[l]+1]==idt[id[r]]&&u[id[l]].k!=u[id[l]+1].k)cout<<"Yes\n";
		else cout<<"No\n";
	}
	return 0;
}
```