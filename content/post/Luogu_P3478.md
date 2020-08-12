+++
title = 'Luogu P3478 [POI2008]STA-Station（换根 DP）'
date = 2020-08-13T03:30:05+08:00
categories = ['题解']
tags = ['换根 DP']
+++

[题目链接](https://www.luogu.com.cn/problem/P3478)

{{% question %}}
给定一个 $n$ 个点的树，请求出一个结点，使得以这个结点为根时，所有结点的深度之和最大。

$1 \leq n \leq 10^6$
{{% /question %}}

<!--more-->

## 简要做法

换根法模板

假设我们知道以 u 为根时的答案

当我们把根变为 u 的某个儿子 v 时，v 的所有儿子的深度都会 - 1 ，不在 v 的子树上的节点深度都会 + 1

$$
\begin{aligned}
f(v) &= f(u) - size_v + (n-size_v)\\
&= f(u) + n - 2 \times size_v
\end{aligned}
$$

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

typedef long long ll;

const ll N = 1e6 + 5;

ll n;
ll head[N], num_edge;
ll f[N], depth[N], size[N];
ll ans, res;

struct Node
{
    ll next, to;
} edge[N << 1];

void add_edge(ll u, ll v)
{
    edge[++num_edge] = Node{head[u], v};
    head[u] = num_edge;
}

ll read()
{
    ll x = 0, f = 1;
    char ch = getchar();
    while ('0' > ch or ch > '9')
        f = ch == '-' ? -1 : 1, ch = getchar();
    while ('0' <= ch and ch <= '9')
        x = x * 10 + ch - 48, ch = getchar();
    return x * f;
}

void dfs_1(ll u, ll fa)
{
    size[u] = 1;
    depth[u] = depth[fa] + 1;
    for (ll i = head[u]; i; i = edge[i].next)
    {
        ll v = edge[i].to;
        if (v != fa)
            dfs_1(v, u), size[u] += size[v];
    }
}

void dfs_2(ll u, ll fa)
{
    for (ll i = head[u]; i; i = edge[i].next)
    {
        ll v = edge[i].to;
        if (v != fa)
            f[v] = f[u] + n - 2 * size[v], dfs_2(v, u);
    }
}

signed main()
{
    n = read();
    for (ll i = 1, u, v; i < n; i++)
        u = read(), v = read(), add_edge(u, v), add_edge(v, u);
    dfs_1(1, 0);
    for (ll i = 1; i <= n; i++)
        f[1] += depth[i];
    dfs_2(1, 0);
    for (ll i = 1; i <= n; i++)
        if (ans < f[i])
            ans = f[i], res = i;
    printf("%lld", res);
    return 0;
}
```
