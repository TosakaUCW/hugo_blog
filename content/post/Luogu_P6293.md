+++
title = 'Luogu P6293 [eJOI2017]经验（树形 DP）'
date = 2020-10-05T17:25:35+08:00
categories = ['题解']
tags = ['树形DP', 'DP']
+++

[题目链接](https://www.luogu.com.cn/problem/P6293)

{{% question %}}

给定一棵有根树，带点权，现要求将这棵树剖成若干条链，使得每条链上点权极差之和最大

$1 \le n \le 10^5$

{{% /question %}}

<!--more-->

## 简要做法

有一个性质，对于一条不是单增或单降的链，将其拆成若干条单调的链会使贡献大于等于拆前的链

考虑有一个单增的数列 $a_n$ 和一个单降的数列 $b_m$，拼在一起，形成一个山峰状

其中我们有 $a_1 < a_n$ 和 $b_1 < b_m$

如果不拆开来，贡献为 $max(a_n,b_m) - min(a_1,b_1)$

拆开来贡献为 $a_n - a_1 + b_m - b_1$

拆开来的贡献总是大于等于不拆开来的贡献

有这个性质就很好 DP 啦

设 $f(u)$ 为当前链单增的最大贡献， $g(u)$ 则为单降的最大贡献

当然这俩方向都是统一的，代码里方向是从上向下的

那么就是从每个满足条件的儿子继承过来或者自己独立出来

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

#define int long long

const int N = 1e5 + 5;
const int M = N << 1;

int n;
int w[N], f[N], g[N];
int head[N], num_edge;

struct Node
{
    int next, to;
} edge[M];

void add_edge(int u, int v) { edge[++num_edge] = Node{head[u], v}, head[u] = num_edge; }

int read()
{
    int x = 0, f = 1;
    char ch = getchar();
    while ('0' > ch or ch > '9')
        f = ch == '-' ? -1 : 1, ch = getchar();
    while ('0' <= ch and ch <= '9')
        x = x * 10 + ch - 48, ch = getchar();
    return x * f;
}

void dfs(int u, int fa)
{
    int k = 0;
    for (int i = head[u], v; i; i = edge[i].next)
        if ((v = edge[i].to) != fa)
            dfs(v, u), k += std::max(f[v], g[v]);
    f[u] = g[u] = k;
    for (int i = head[u], v; i; i = edge[i].next)
        if ((v = edge[i].to) != fa and w[u] >= w[v])
            f[u] = std::max(f[u], k - std::max(f[v], g[v]) + w[u] - w[v] + f[v]);
        else if (v != fa and w[u] <= w[v])
            g[u] = std::max(g[u], k - std::max(f[v], g[v]) + w[v] - w[u] + g[v]);
}

signed main()
{
    n = read();
    for (int i = 1; i <= n; i++)
        w[i] = read();
    for (int i = 1, u, v; i < n; i++)
        u = read(), v = read(), add_edge(u, v), add_edge(v, u);
    dfs(1, 0);
    return printf("%lld", std::max(f[1], g[1])), 0;
}
```
