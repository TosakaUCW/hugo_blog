+++
title = "Luogu P5002 专心OI - 找祖先（图论）"
categories = ["题解"]
tags = ["图论"]
date = "2019-02-03T23:25:30+08:00"
description = ""
aliases = ["/post/Luogu_P5002", "Luogu_P5002"]
+++

[题目链接](https://www.luogu.org/problemnew/show/P5002)

{{% question %}}
给定 1 棵 N 个节点的树，并钦定 M 个点

对于被钦定的点 \`u\`，求有多少组点对 \`(i,j)\` 的 LCA 是 \`u\`

\`N \leq 10^4\`

\`M \leq 5 \times 10^4\`
{{% /question %}}

<!--more-->

## 简要做法

首先对于 \`u\` 这个点为 LCA 时，点对肯定是在不同的子树中

- \`(i,j)\` 的其中一个点是 \`u\` ，\`ans = size_u \times 2 - 1\`

- \`(i,j)\` 中不含 \`u\`，

    设 \`u\` 一共有  \`k\` 棵子树

    \`ans = \sum_{i=1}^k \sum_{j=1}^k size_i \times size_j (i \ne j)\`

    \`ans = \sum_{i=1}^k \sum_{j=1}^k size_i \times size_j - \sum_{i=1}^k size_i^2\`

    \`ans = \sum_{i=1}^k size_i \times (size_u - 1) - \sum_{i=1}^k size_i^2\`

    \`ans = (size_u - 1) \times (size_u - 1) - \sum_{i=1}^k size_i^2\`

    \`ans = (size_u - 1)^2 - \sum_{i=1}^k size_i^2\`

两个情况加起来

\`ans = size_u \times 2 - 1+(size_u - 1)^2 - \sum_{i=1}^k size_i^2\`

\`ans = size_u^2 - \sum_{i=1}^k size_i^2\`

dfs 一下统计答案即可

## 参考代码

```c++
#include <stdio.h>
#include <algorithm>

const int N = 1e4 + 5;
const int M = 2e4 + 5;
const int LOG2N = 15;

int n, root, m;
int head[N], num_edge;
int size[N], sum[N], ans[N];

struct Node
{
    int next, to;
} edge[M];

void add_edge(int u, int v)
{
    edge[++num_edge].next = head[u];
    edge[num_edge].to = v;
    head[u] = num_edge;
}

void dfs(int u, int father)
{
    size[u] = 1;
    for (int i = head[u]; i; i = edge[i].next)
    {
        int v = edge[i].to;
        if (v != father)
        {
            dfs(v, u);
            size[u] += size[v];
            sum[u] += size[v] * size[v];
        }
    }
    ans[u] = size[u] * size[u] - sum[u];
}

int main()
{
    scanf("%d%d%d", &n, &root, &m);
    for (int i = 1; i < n; i++)
    {
        int u, v;
        scanf("%d%d", &u, &v);
        add_edge(u, v);
        add_edge(v, u);
    }
    dfs(root, 0);
    for (int i = 1; i <= m; i++)
    {
        int x;
        scanf("%d", &x);
        printf("%d\n", ans[x]);
    }
    return 0;
}
```