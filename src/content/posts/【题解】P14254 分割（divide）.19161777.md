---
title: 【题解】分割
published: 2025-10-23
description: 题解
tags: [题解]
category: 算法
draft: false
---

想了想，感觉这道题还是总结一下为好。

这个题需要涉及大量证明，也是很恶心人了。

引理一：当第 $1$ 个点选择了深度为 $i$，那后续所有节点的深度就只能为 $i$。

证明：因为选择的点的深度是不降得，所以不能选深度小于 $i$ 的点，如果后续有节点选择了深度大于 $i$，则会造成 $S$ 的并集会没有 $i$（因为你这个选择了深度小于 $i$ 的节点造成的集合里必定没有 $i$），然而 $s_1$ 是一定包括 $i$ 的，所以就不相等了，则不能选择深度大于 $i$ 的节点。

现在我们已经知道这个点，他要选就要在同一层里选了，那么我们直接一层一层的枚举就行了。

有个错误的贪心：就是在这一层中，你必须选择一个这些节点中所属子树中可遍历最大深度最小的节点。

这个贪心为什么看起来比较对呢，因为如果你选择上面所阐述的这种节点他的 $S$ 集合范围是最小的。

但这个贪心错在哪呢？有的节点它可以并到 $S_{k+1}$ 上，以至于他的最小深度不会被记录。

那我们枚举一个深度中的所有节点，让枚举到的这个节点作为 $1$ 号节点，那以这个节点为一号节点的合法序列有那些呢？

我们设一个节点的所属子树最大向下拓展深度为 $mx_i$。

引理二：对于 $2\dots k+1$ 号选择的点，他们的 $mx$ 值必须大于等于 $mx_1$。

证明：如果存在节点的 $mx$ 值小于 $mx_1$，那么就会出现 $S_1$ 有 $mx_1$，然而 $S$ 的并集却不存在 $mx_1$，因为那个 $mx$ 值小于 $mx_1$ 的节点所形成的集合中就没有 $mx_1$。

引理三：对于 $2\dots k+1$ 号选择的点，必定有一个节点的 $mx$ 值等于 $mx_1$。

证明：就是防止 $S$ 的并集从 $mx_1$ 继续向下拓展。

我们把这个序列分成两种：

1. $mx_i=mx_1$ 的点 $i$ 在 $k+1$ 棵树中。

2. 另一种情况。

对于第一种情况：

引理四：在这一种情况中，大于 $mx_1$ 的个数等于 $k-1$。

证明：

如果大于 $mx_1$ 的个数大于 $k-1$，则会导致多余的在前面已经填满 $mx_i>mx_1$ 的数的情况下，有 $mx_i>mx_1$ 的数到第 $k+1$ 棵树中，导致第 $k+1$ 树中的最大深度大于 $mx_1$。

如果大于 $mx_1$ 的个数小于 $k-1$，这个会导致有 $mx_i=mx_1$ 的数填进前 $k$ 个树，所以“$mx_i=mx_1$ 的点 $i$ 在 $k+1$ 棵树中”的假设就不成立了。

这种情况的话，那么已经固定好了第了要从 $k-1$ 个点中选 $k-1$ 个了，所以答案累加 $(k-1)!$。

对于第二种情况：

引理五：一定要注意大于 $mx_1$ 的个数一定要大于 $k$。

证明：就是上面说的引理二的拓展。

那么答案直接累加在大于等于 $mx_1$ 的数中选 $k-1$ 排列的方案数减去大于 $mx_1$ 的数中选 $k-1$ 排列的方案数。

对于维护每一层大于 $mx_i$ 的个数，就是建一个对于 $mx$ 值的树状数组即可，记得一个一个减回去，不要```memset```，大佬们都直接排序。

说在最后：

在做这道题的时候，我一开始想的是分成要么它可以在第 $k+1$ 棵树上有 $mx_1$，要么不可以两种计数。

然后不可以的时候就直接在他相等的个数中选一个及其可以在的位置乘上其他点的排列，实际上这个东西会冲突，所以放弃了。

关于时间复杂度，有树状数组一个 $log$，但无伤大雅，如果没有树状数组就是 $O(n),因为每一层的数是不重复的，且加起来一共就 $n$ 个。

```
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const ll N = 1e6 + 5;
const ll mod = 998244353;
ll n;
ll dep[N], mx[N];
vector<ll> e[N];
vector<ll> ep[N];
void dfs(ll x) {
    mx[x] = dep[x];
    for (auto v : e[x]) {
        dep[v] = dep[x] + 1;
        dfs(v);
        mx[x] = max(mx[x], mx[v]);
    }
    ep[dep[x]].push_back(x);
}
ll jc[N * 2], inv[N * 2];
ll poww(ll a, ll b) {
    ll ans = 1;
    while (b) {
        if (b & 1)
            ans = (ans * a) % mod;
        a = (a * a) % mod;
        b >>= 1;
    }
    return ans;
}
ll A(ll n, ll m) {
	if(n<m)return 0;
    return jc[n] * inv[n - m] % mod;
}
ll tr[N];
#define lowbit(i) (i & -i)
void modify(ll x, ll y) {
    for (ll i = x; i <= n; i += lowbit(i)) tr[i] = (tr[i] + y);
}
ll ask(ll x) {
    ll cnt = 0;
    for (ll i = x; i >= 1; i -= lowbit(i)) cnt += tr[i];
    return cnt;
}
ll query(ll x) {
    return ask(n) - ask(x);
}
int main() {
    ll k;
    cin >> n >> k;
    jc[1] = inv[1] = 1;
    inv[0] = 1;
    for (ll i = 2; i <= n; i++) {
        jc[i] = jc[i - 1] * i % mod;
        inv[i] = poww(jc[i], mod - 2);
    }
    for (ll i = 2; i <= n; i++) {
        ll x;
        cin >> x;
        e[x].push_back(i);
    }
    dep[1] = 1;
    dfs(1);
    ll ans = 0;
    for (ll i = 1; i <= n; i++) {
        for (auto j : ep[i]) modify(mx[j], 1);
        for (auto j : ep[i]) {
            ll p = query(mx[j]);
            ll op = ask(mx[j]) - ask(mx[j] - 1);
            if (op >= 2) {
                ll cnt = 0;
                if(p+op-1>=k)  cnt = (cnt + A(p + op - 1, k - 1) - A(p, k - 1) + mod) % mod;
                if (p == k - 1)
                    cnt = (cnt + jc[k - 1]) % mod;
                ans = (ans + cnt) % mod;
            }
        }
        for (auto j : ep[i]) modify(mx[j], -1);
    }
    cout << ans << '\n';
    return 0;
}
```