+++
title = "Luogu P4151 [WC2011]最大XOR和路径（线性基）"
categories = ["题解"]
tags = ["线性基"]
date = "2019-02-03T23:25:30+08:00"
description = ""
aliases = ["/post/Luogu_P4151", "Luogu_P4151"]
+++

[题目链接](https://www.luogu.org/problemnew/show/P4151)

{{% question %}}
给定一张无向带权图，有重边和自环

求从 1 走到 N 的路径最大异或和
{{% /question %}}

<!--more-->

## 简要做法

假设当前已经找到了一条 1 走到 N 的非最优解路径

这时想到题目中提到`有重边和自环`

于是考虑利用异或的性质进行增广

一条链 + 一个环 可以对答案贡献一个环的异或和

因为链走过去走回来异或下就抵消了

所以搜索一下枚举每个环加入线性基即可

话不多说看代码

~~然后就水了一道黑题~~

## 参考代码

```c++
#include <stdio.h>

typedef long long LL;

const int N = 5e4 + 5;
const int M = 2e5 + 5;

int n, m, num_edge, head[N];
LL dis[N];
bool vis[N];

struct Edge
{
    int next, to;
    LL dis;
} edge[M];

void add_edge(int u, int v, LL dist)
{
    edge[++num_edge].next = head[u];
    edge[num_edge].to = v;
    edge[num_edge].dis = dist;
    head[u] = num_edge;
}

class Linear_Base
{
  private:
    static const int LogN = 63;
    LL a[LogN + 5];

  public:
    void insert(LL x)
    {
        for (int i = LogN; i >= 0; i--)
            if (x & (1LL << i))
                if (!a[i])
                {
                    a[i] = x;
                    return;
                }
                else
                    x ^= a[i];
    }
    LL query_max(LL x)
    {
        LL res = x;
        for (int i = LogN; i >= 0; i--)
            if ((res ^ a[i]) > res)
                res ^= a[i];
        return res;
    }
} LB;

void DFS(int u, LL res)
{
    dis[u] = res;
    vis[u] = true;
    for (int i = head[u]; i; i = edge[i].next)
    {
        int v = edge[i].to;
        LL dist = edge[i].dis;
        if (!vis[v])
            DFS(v, res ^ dist);
        else
            LB.insert(dis[v] ^ res ^ dist);
    }
}

int main()
{
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= m; i++)
    {
        int u, v;
        LL dist;
        scanf("%d%d%lld", &u, &v, &dist);
        add_edge(u, v, dist);
        add_edge(v, u, dist);
    }
    DFS(1, 0);
    printf("%lld", LB.query_max(dis[n]));
    return 0;
}
```