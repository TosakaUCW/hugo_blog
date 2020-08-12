+++
title = "CodeVS 1391 伊吹萃香（分层图最短路）"
categories = ["题解"]
tags = ["分层图最短路"]
date = "2019-03-11T23:25:30+08:00"
description = ""
aliases = ["/post/CodeVS_1391", "/CodeVS_1391"]
+++

[题目链接](http://codevs.cn/problem/1391)

{{% question %}}
给定一个图

n个黑白点，m条有向边

每个节点初始有一个质量weight[i]

走过每一条边需要消耗一定量的体力以及1个单位的时间

由于黑白点的存在，走过每条路需要消耗的体力也就产生了变化，假设一条道路两端路口黑白洞的质量差为delta：

1. 从白到黑，消耗的体力值减少delta，若该条路径消耗的体力值变为负数的话，取为0

2. 从黑到白，消耗的体力值增加delta

3. 同色，消耗的体力值无变化

每过1个单位时间，就把每个点的黑白颜色相反

可以选择在一个点上停留1个单位的时间，如果是白点，则不消耗体力，否则消耗cost[i]的体力

1 <= N <= 5e3

1 <= M <= 3e4
{{% /question %}}

<!--more-->

## 简要做法

每个点拆成黑白两种情况

对于点 x ，用 2x 和 2x-1 表示颜色情况

再按题意拉边跑一遍最短路即可

## 参考代码

```c++
#include <math.h>
#include <memory.h>
#include <stdio.h>
#include <algorithm>
#include <queue>

const int N = 5e3 + 5;
const int M = 3e4 + 5;

int n, m;
int color[N], weight[N], cost[N];
int num_edge, head[2 * N], dis[2 * N];
bool vis[2 * N];
std::queue<int> Q;

struct Node
{
    int next, to, dis;
} edge[4 * M];

void add_edge(int u, int v, int dis)
{
    edge[++num_edge].next = head[u];
    edge[num_edge].to = v;
    edge[num_edge].dis = dis;
    head[u] = num_edge;
}

void SPFA()
{
    int s;
    if (color[1] == 0)
        s = 2;
    else
        s = 1;
    memset(dis, 127, sizeof dis);
    Q.push(s);
    dis[s] = 0, vis[s] = true;
    while (!Q.empty())
    {
        int u = Q.front();
        Q.pop();
        vis[u] = false;
        for (int i = head[u]; i; i = edge[i].next)
        {
            int v = edge[i].to;
            int dist = edge[i].dis;
            if (dis[v] > dis[u] + dist)
            {
                dis[v] = dis[u] + dist;
                if (!vis[v])
                    Q.push(v), vis[v] = true;
            }
        }
    }
}

int main()
{
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++)
        scanf("%d", &color[i]);
    for (int i = 1; i <= n; i++)
        scanf("%d", &weight[i]);
    for (int i = 1; i <= n; i++)
        scanf("%d", &cost[i]);
    for (int i = 1; i <= m; i++)
    {
        int u, v, dis;
        scanf("%d%d%d", &u, &v, &dis);
        if (color[u] == color[v])
        {
            add_edge(2 * u, 2 * v - 1, dis);
            add_edge(2 * u - 1, 2 * v, dis);
        }
        else
        {
            int delta = abs(weight[u] - weight[v]);
            add_edge(2 * u - 1, 2 * v - 1, dis + delta);
            add_edge(2 * u, 2 * v, std::max(0, dis - delta));
        }
    }
    for (int i = 1; i <= n; i++)
    {
        add_edge(2 * i, 2 * i - 1, 0);
        add_edge(2 * i - 1, i * 2, cost[i]);
    }
    SPFA();
    printf("%d", std::min(dis[2 * n], dis[2 * n - 1]));
    return 0;
}
```