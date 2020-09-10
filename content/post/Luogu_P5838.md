+++
title = 'Luogu P5838 P5838 [USACO19DEC]Milk Visits G（树链剖分，二分）'
date = 2020-09-10T18:19:26+08:00
categories = ['题解']
tags = ['树链剖分', '二分']
+++

[题目链接](https://www.luogu.com.cn/problem/P5838)

{{% question %}}

给定一棵树，$M$ 次询问，树上两点之间的简单路径上是否有某种颜色的节点。

$1 \le N \le 10^5$

$1 \le M \le 10^5$

{{% /question %}}

<!--more-->

## 简要做法

这题是树！先剖了再说

那么问题就变为了：在跳重链的时候，看看当前链上有没有颜色为 $k$ 的点

看起来就很不优美，事实上对于每次操作我们只关注那种颜色的点

所以，把每种颜色的点的 dfn 序开个 vector 存起来

那么就变成了一个经典问题：数组中是否存在一个数 $x$，满足 $dfn[top[u]] \le x \le dfn[u]$

二分即可

最坏时间复杂度 $O(n{log}^{2}n)$

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>
#include <vector>

const int N = 1e5 + 5;
const int M = N << 1;

int n, m;
int a[N];
int head[N], num_edge;
int fa[N], son[N], top[N], dfn[N], idx[N], depth[N], size[N];
std::vector<int> v[N];

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

void dfs1(int u, int fa)
{
    ::fa[u] = fa, size[u] = 1, depth[u] = depth[fa] + 1;
    for (int i = head[u], v; i; i = edge[i].next)
        if ((v = edge[i].to) != fa)
            dfs1(v, u), size[u] += size[v], son[u] = size[v] > size[son[u]] ? v : son[u];
}

void dfs2(int u)
{
    dfn[u] = ++dfn[0], idx[dfn[u]] = u, top[u] = u == son[fa[u]] ? top[fa[u]] : u;
    if (son[u])
        dfs2(son[u]);
    for (int i = head[u], v; i; i = edge[i].next)
        if ((v = edge[i].to) != fa[u] and v != son[u])
            dfs2(v);
}

int main()
{
    n = read(), m = read();
    for (int i = 1; i <= n; i++)
        a[i] = read();
    for (int i = 1, u, v; i < n; i++)
        u = read(), v = read(), add_edge(u, v), add_edge(v, u);
    dfs1(1, 0), dfs2(1);
    for (int i = 1; i <= n; i++)
        v[a[idx[i]]].push_back(i);
    for (int x, y, k; m--;)
    {
        x = read(), y = read(), k = read();
        bool flag = false;
        for (; top[x] != top[y]; x = fa[top[x]])
        {
            if (dfn[top[x]] < dfn[top[y]])
                std::swap(x, y);
            std::vector<int>::iterator it = std::lower_bound(v[k].begin(), v[k].end(), dfn[top[x]]);
            if (it != v[k].end() and *it <= dfn[x])
            {
                flag = true;
                break;
            }
        }
        if (!flag)
        {
            if (dfn[x] > dfn[y])
                std::swap(x, y);
            std::vector<int>::iterator it = std::lower_bound(v[k].begin(), v[k].end(), dfn[x]);
            if (it != v[k].end() and *it <= dfn[y])
                flag = true;
        }
        printf("%d", flag);
    }
    return 0;
}
```
