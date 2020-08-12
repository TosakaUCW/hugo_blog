+++
title = 'Luogu P3047 [USACO12FEB]Nearby Cows G（换根 DP）'
date = 2020-08-13T03:34:25+08:00
categories = ['题解']
tags = ['换根 DP']
+++

[题目链接](https://www.luogu.com.cn/problem/P3047)

{{% question %}}
给你一棵 $n$ 个点的树，点带权，对于每个节点求出距离它不超过 $k$ 的所有节点权值和 $m_i$

$1 \leq n \leq 10^5$
{{% /question %}}

<!--more-->

## 简要做法

考虑设 $f(u,j)$ 表示到 $u$ 这个点距离为 j 的点集的权值和

$$f(u,j)=\sum_{v\in{u.son}} f(v,j-1)$$

当我们换根 dp 时，还需要注意 $f(u,j)$ 从 $f(v,j-1)$ 更新过来时，$f(v,j-1)$ 包含了 $f(u,j-2)$ 注意减去

两遍 dfs 换根 dp 即可

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

const int N = 1e5 + 5;
const int K = 20 + 5;

int n, k;
int a[N], f[N][K];
int head[N], num_edge;

struct Node
{
    int next, to;
} edge[N << 1];

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

void dfs1(int u, int fa)
{
    for (int i = head[u]; i; i = edge[i].next)
    {
        int v = edge[i].to;
        if (v == fa)
            continue;
        dfs1(v, u);
        for (int i = 1; i <= k; i++)
            f[u][i] += f[v][i - 1];
    }
}

void dfs2(int u, int fa)
{
    for (int i = head[u]; i; i = edge[i].next)
    {
        int v = edge[i].to;
        if (v == fa)
            continue;
        for (int i = k; i >= 2; i--)
            f[v][i] -= f[v][i - 2];
        for (int i = 1; i <= k; i++)
            f[v][i] += f[u][i - 1];
        dfs2(v, u);
    }
}

int main()
{
    n = read(), k = read();
    for (int i = 1, u, v; i < n; i++)
        u = read(), v = read(), add_edge(u, v), add_edge(v, u);
    for (int i = 1; i <= n; i++)
        f[i][0] = read();
    dfs1(1, 0);
    dfs2(1, 0);
    for (int i = 1; i <= n; i++)
    {
        int res = 0;
        for (int j = 0; j <= k; j++)
            res += f[i][j];
        printf("%d\n", res);
    }

    return 0;
}
```
