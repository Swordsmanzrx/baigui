---
title: 【题解】 历史的研究
published: 2025-08-07
description: 题解
tags: [题解]
category: 算法
draft: false
---

Atcoder：[歴史の研究](https://atcoder.jp/contests/joisc2014/tasks/joisc2014_c)

Luogu: [歴史の研究](https://www.luogu.com.cn/problem/AT_joisc2014_c)

# 题意：

给你一个长度为 $n$ 的序列 $a$。

有 $q$ 个查询，每个查询有两个整数 $l,r$，要求 $[l,r]$ 区间内 $max\{a_i*T_{a_i} \}$，$T_{a_i}$ 表示 $a_i$ 在区间 $[l,r]$ 中的出现次数。

# 思路

看解区中大部分都是回滚莫队，其实这道题是一道十分好的分块。

考虑怎末分块。

## Step one

$\large{离散化！！！}$

```
void discrete(){
	for(int i=1;i<=n;i++)b[i]=a[i];
	sort(b+1,b+1+n);
	for(int i=1;i<=n;i++)
		if(b[i]!=b[i-1]||i==1)
			sdf[++to]=b[i];
	for(int i=1;i<=n;i++)a[i]=lower_bound(sdf+1,sdf+1+to,a[i])-sdf;
}
```

我之前一直都喜欢不先将 $a_i$ 换成离散化的值，但从这道题以后，我改掉了这个习惯，为什莫呢？如果你打一个 $qry$ 函数来直接求 $a_i$ 离散后的值，是不是每一次都需要运行一下 $lower$ $bound$ 这个函数，这个函数本质上是二分啊，他每一次都有 $O(log_n)$ 的复杂度！！

这样稍有不慎就会 $\huge{\color{black}TLE}$。

## Step two

预处理。

### 预处理一

分块嘛。

第一个预处理就是把序列分成许多块嘛。

这里给一些板子：

1. 常用的分块大小 $size=\left \lceil\sqrt{n}\right\rceil$。

2. 第 $i$ 个数属于第 $\left \lfloor \frac{(i-1)}{size}\right \rfloor+1$ 块。

3. 总块数 $len=\left \lfloor \frac{(n-1)}{size}\right \rfloor +1$。

4. 第 $i$ 块的左端点 $L_i=(i-1)\times size+1$。

5. 第 $i(i\ne len)$ 块的右端点 $R_i=size\times i$。

6. 特注：第 $len$ 块的左端点是 $n$（这还用特注？（不是。

```
si=sqrt(n);
for(int i=1;i<=n;i++)id[i]=(i-1)/si+1;
int le=(n-1)/si+1;
for(int i=1;i<=le;i++){
	L[i]=(i-1)*si+1;
	R[i]=i*si;
}
R[le]=n;
```

### 预处理二

用 $g_{i,j}$ 表示从第 $1$ 块到了第 $i$ 块，$j$ 的累计出现次数。

```
for(int i=1;i<=le;i++){
	for(int j=L[i];j<=R[i];j++)++cu[a[j]];// cu 表示第i个数的累计出现次数
	for(int j=1;j<=to;j++)g[i][j]=cu[j];
} 
```

复杂度 $O(n\sqrt{n})$。

### 预处理三

用 $f_{i,j}$ 表示从第 $i$ 块到第 $j$ 块这段整体的答案。

```
for(int i=1;i<=le;i++){
	memset(cu,0,sizeof(cu));
	ll an=0;
	for(int j=i;j<=le;j++){
		for(int k=L[j];k<=R[j];k++){
			++cu[a[k]];
			an=max(an,(ll)cu[a[k]]*sdf[a[k]]);// 在这里我做的时候不慎将sdf[a[k]] 写成了 sdf[a[i]]，然后。。。。所以打代码一定要小心啊！！！
		}
		f[i][j]=an;
	}
}
```

其实你在这里将 $cu$ 数组搞回原型的时候，也可以一个一个的减回去，但只要复杂度不超，$memset$ 还是最方便的。

## Step tree

就是正常的分块了。

感觉没啥好说的，就是强调一下，这里用桶统计答案后一定要一个一个的减回去清空，因为 $memset$ 的复杂度是 $O(n)$ 的，这十分的可怕，有人会认为既然前面都循环了一遍了都没事，那 $memset$ 一定也没事（没错就是我），可是 $memset$ 是将那个数组的设置大小的 $O(n)$，但这里循环的只是一部分，这道题会不会 $TLE$ 我不清楚，因我的代码似乎不是这样似的。

### 分类讨论一

当 $l$ 和 $r$ 在同一块内。

直接统计答案。（ 嗯嗯，十分符合分块部分暴力思想。

```
if(id[l]==id[r]){
	ll ans=0;
	for(int i=l;i<=r;i++){
		++cu[(a[i])];
		ans = max(ans,(ll) cu[(a[i])]*sdf[a[i]]);
	}
	for(int i=l;i<=r;i++)--cu[(a[i])];
	cout<<ans<<"\n";		
    continue;
}
```

### 分类讨论二

首先还是传统的分块思想。

答案还是只分三部分。

1. 在 $l$ 到 $r$ 之间的整块内的元素（这里笔者为寻方便不考虑 $l,r$ 在分块的边界的事情，整块只指 $l$ 所属块后面的那个块到 $r$ 所属块前面的那个块）。

2. $l$ 到 $l$ 所属块右端点 内的元素。

3. $r$ 到 $r$ 所属块 左端点内的元素。

由于我们已经预处理出了所有整块的答案，所以部分一的答案直接从预处理数组中取出来即可。

剩下的似乎也没有什莫好说的了。

```
ll ans=f[id[l]+1][id[r]-1];
for(int i=l;i<=R[id[l]];i++){
	++cu[(a[i])];
	ll sz=cu[(a[i])]+g[id[r]-1][(a[i])]-g[id[l]][(a[i])] ;
	ans=max(ans,sz*sdf[a[i]]);
}
for(int i=L[id[r]];i<=r;i++){
	++cu[(a[i])];
	ll sz=cu[(a[i])]+g[id[r]-1][(a[i])]-g[id[l]][(a[i])] ;
	ans=max(ans,sz*sdf[a[i]]);
}
cout<<ans<<"\n";
for(int i=l;i<=R[id[l]];i++)--cu[(a[i])];
for(int i=L[id[r]];i<=r;i++)--cu[(a[i])];
```

# $\huge{End}$

分块大法好。



```
#include<bits/stdc++.h>
using namespace std;
typedef long long ll; 
const int N=1e5+5;;
const int T=320;
int n,q;
int L[T];
int R[T];
ll f[T][T];
int id[N];
int g[T][N];
int a[N],b[N];
int cu[N];
int sdf[N],to=0;
int si;
int qry(int x){
	return lower_bound(sdf+1,sdf+1+to,x)-sdf;
}
void discrete(){
	for(int i=1;i<=n;i++)b[i]=a[i];
	sort(b+1,b+1+n);
	for(int i=1;i<=n;i++)
		if(b[i]!=b[i-1]||i==1)
			sdf[++to]=b[i];
	for(int i=1;i<=n;i++)a[i]=qry(a[i]);
}
int main(){
	ios::sync_with_stdio(0);
	cin.tie(0);
	cout.tie(0);
	cin>>n>>q;
	for(int i=1;i<=n;i++)cin>>a[i];
	discrete();	
	si=sqrt(n);
	for(int i=1;i<=n;i++)id[i]=(i-1)/si+1;
	int le=(n-1)/si+1;
	for(int i=1;i<=le;i++){
		L[i]=(i-1)*si+1;
		R[i]=i*si;
	}
	R[le]=n;
	for(int i=1;i<=le;i++){
		for(int j=L[i];j<=R[i];j++)++cu[(a[j])];
		for(int j=1;j<=to;j++)g[i][j]=cu[j];
	} 
	for(int i=1;i<=le;i++){
		memset(cu,0,sizeof(cu));
		ll an=0;
		for(int j=i;j<=le;j++){
			for(int k=L[j];k<=R[j];k++){
				++cu[(a[k])];
				an=max(an,(ll)cu[(a[k])]*sdf[a[k]]);
			}
			f[i][j]=an;
		}
	}
	memset(cu,0,sizeof(cu));
	while(q--){
		int l,r;
		cin>>l>>r;
		if(id[l]==id[r]){
			ll ans=0;
			for(int i=l;i<=r;i++){
				++cu[(a[i])];
				ans = max(ans,(ll) cu[(a[i])]*sdf[a[i]]);
			}
			for(int i=l;i<=r;i++)--cu[(a[i])];
			cout<<ans<<"\n";
			continue;
		}
		ll ans=f[id[l]+1][id[r]-1];
		for(int i=l;i<=R[id[l]];i++){
			++cu[(a[i])];
			ll sz=cu[(a[i])]+g[id[r]-1][(a[i])]-g[id[l]][(a[i])] ;
			ans=max(ans,sz*sdf[a[i]]);
		}
		for(int i=L[id[r]];i<=r;i++){
			++cu[(a[i])];
			ll sz=cu[(a[i])]+g[id[r]-1][(a[i])]-g[id[l]][(a[i])] ;
			ans=max(ans,sz*sdf[a[i]]);
		}
		cout<<ans<<"\n";
		for(int i=l;i<=R[id[l]];i++)--cu[(a[i])];
		for(int i=L[id[r]];i<=r;i++)--cu[(a[i])];
	}
	return 0;
}
```

![img](https://img2024.cnblogs.com/blog/3422024/202508/3422024-20250807153540893-1694979364.png)