+++
title = 'Luogu P2812 校园网络【[USACO]Network of Schools加强版】(缩点）'
date = 2020-08-13T03:20:46+08:00
categories = ['题解']
tags = ['缩点']
+++

[题目链接](https://www.luogu.com.cn/problem/P2812)

{{% question %}}
添加最少的有向边使给定的 DAG 强联通
{{% /question %}}

<!--more-->

## 简要做法

考虑强联通图和 DAG，有一个特征，强联通图不包含

1. 出度为 0 的点
2. 入度为 0 的点

考虑在这些点间拉边，每当我们添加一条 (u, v)，的边，就减少了两个这样的点

所以答案为两者取 max

注意特判只有一个点的情况

先跑 tarjan 缩点，然后统计下出入度即可

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>
#include <stack>

const int N = 5e6 + 5;

int n;
int head[N], num_edge;

int num_scc, timer;
int dfn[N], low[N];
int belong[N];
bool inStack[N];
std::stack<int> S;

int ind[N], oud[N];

struct Node
{
    int next, to;
} edge[N];

void add_edge(int u, int v)
{
    edge[++num_edge] = Node{head[u], v};
    head[u] = num_edge;
}

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

void tarjan(int u)
{
    S.push(u), inStack[u] = true;
    dfn[u] = low[u] = ++timer;
    for (int i = head[u]; i; i = edge[i].next)
    {
        int v = edge[i].to;
        if (!dfn[v])
            tarjan(v), low[u] = std::min(low[u], low[v]);
        else if (inStack[v])
            low[u] = std::min(low[u], dfn[v]);
    }
    if (dfn[u] == low[u])
    {
        ++num_scc;
        int x;
        do
        {
            x = S.top();
            S.pop();
            inStack[x] = false;
            belong[x] = num_scc;
        } while (x != u);
    }
}

int main()
{
    n = read();
    for (int i = 1; i <= n; i++)
        for (int x = read(); x; x = read())
            add_edge(i, x), add_edge(x, i);
    for (int i = 1; i <= n; i++)
        if (!dfn[i])
            tarjan(i);
    for (int u = 1; u <= n; u++)
        for (int i = head[u]; i; i = edge[i].next)
        {
            int v = edge[i].to;
            if (belong[u] != belong[v])
                oud[belong[u]]++, ind[belong[v]]++;
        }
    int res1 = 0, res2 = 0;
    for (int i = 1; i <= num_scc; i++)
        res1 += (ind[i] == 0), res2 += (oud[i] == 0);
    res2 = std::max(res1, res2);
    if (num_scc == 1)
        res1 = 1, res2 = 0;
    printf("%d\n%d", res1, res2);
    return 0;
}
```
