+++
title = 'CF 59E Shortest Path（最短路）'
date = 2020-09-05T14:41:41+08:00
categories = ['题解']
tags = ['最短路']
+++

[题目链接](https://codeforces.com/contest/59/problem/E)

{{% question %}}

给定一个包含 N 个点，M 条边的无向图，每条边的边权均为 1 。

再给定 K 个三元组（A，B，C），表示从 A 点走到 B 点后不能往 C 点走。注意三元组是有序的，如可以从 B 点走到 A 点再走到 C。

现在你要在 K 个三元组的限制下，找出 1 号点到 N 号点的最短路径，并输出任意一条合法路径，会有 Check 检查你的输出。

$N \le 3 \times 10^3$

$M \le 2 \times 10^4$

$K \le 1 \times 10^5$

{{% /question %}}

<!--more-->

## 简要做法

一个小 Trick，点边转换

然后正常跑最短路

## 参考代码

```cpp
#include <stdio.h>
#include <queue>
#include <stdlib.h>

const int N = 3e3 + 5;
const int M = 4e4 + 5;

int n, m, k;
int head[N], num_edge;
int dis[N][N], pre[N][N];
int ans[M];
std::vector<std::pair<int, int>> f[N];

struct Node
{
    int next, to;
} edge[M];
struct node
{
    int u, v;
};

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

bool judge(int x, int y, int z)
{
    for (int i = 0, n = f[x].size(); i < n; i++)
        if (f[x][i].first == y and f[x][i].second == z)
            return false;
    return true;
}

void print(int last)
{
    printf("%d\n1 ", dis[last][n]);
    int cnt = 0;
    for (int u = n, tmp; u != 1; tmp = last, last = pre[last][u], u = tmp)
        ans[++cnt] = u;
    for (int i = cnt; i; i--)
        printf("%d ", ans[i]);
}

void bfs()
{
    std::queue<node> Q;
    Q.push(node{0, 1});
    while (!Q.empty())
    {
        int last = Q.front().u, u = Q.front().v;
        Q.pop();
        if (u == n)
            print(last), exit(0);
        for (int i = head[u]; i; i = edge[i].next)
        {
            int v = edge[i].to;
            if (judge(last, u, v) and !dis[u][v])
                pre[u][v] = last,
                dis[u][v] = dis[last][u] + 1,
                Q.push(node{u, v});
        }
    }
}

int main()
{
    n = read(), m = read(), k = read();
    for (int i = 1, u, v; i <= m; i++)
        u = read(), v = read(), add_edge(u, v), add_edge(v, u);
    for (int i = 1, x, y, z; i <= k; i++)
        x = read(), y = read(), z = read(), f[x].push_back(std::make_pair(y, z));
    bfs();
    puts("-1");
    return 0;
}
```
