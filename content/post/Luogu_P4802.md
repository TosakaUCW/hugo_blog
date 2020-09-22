+++
title = 'Luogu P4802 [CCO 2015]路短最（状态压缩，DP）'
date = 2020-09-22T19:18:13+08:00
categories = ['题解']
tags = ['状态压缩', 'DP']
+++

[题目链接](https://www.luogu.com.cn/problem/P4802)

{{% question %}}

给定一张图，点编号从 $0$ 到 $n - 1$，求一条起点为 $0$ 号点 终点为 $n - 1$ 号点的最短路，每个点只能经过一次

$2 \le n \le 18$

{{% /question %}}

<!--more-->

## 简要做法

数据范围想到状压

$f(i,j)$ 表示从起点走到点 $i$，状态为 $j$ 的最长路

dp 即可

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

const int N = 18 + 1;

int n, m, ans;
int dis[N][N], f[N][1 << N];

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

int main()
{
    memset(f, 0xcf, sizeof f), memset(dis, 0xcf, sizeof dis);
    n = read(), m = read();
    for (int i = 1, u, v, w; i <= m; i++)
        u = read(), v = read(), w = read(), dis[u][v] = w;
    for (int i = 1; i < n; i++)
        f[i][1 + (1 << i)] = dis[0][i];
    for (int i = 2; i < (1 << n); i++)
        for (int j = 0; j < n; j++)
            if (i & (1 << j))
                for (int k = 0; k < n; k++)
                    if (j != k and !(i & (1 << k)))
                        f[k][i | (1 << k)] = std::max(f[k][i | (1 << k)], f[j][i] + dis[j][k]);
    for (int i = (1 << (n - 1)) + 1; i < (1 << n); i++)
        ans = std::max(ans, f[n - 1][i]);
    return printf("%d", ans), 0;
}
```
