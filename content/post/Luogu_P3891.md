+++
title = 'Luogu P3891 [GDOI2014]采集资源（DP）'
date = 2020-09-28T20:35:59+08:00
categories = ['题解']
tags = ['DP']
+++

[题目链接](https://www.luogu.com.cn/problem/P3891)

{{% question %}}

有 $n$ 种农民，每种农民有两个属性，造价和生产力

初始有 $m$ 块钱，和 $0$ 个农民，求赚到 $t$ 快钱的最少天数

$N \le 100$

$M, T \le 1000$

数据保证有解

{{% /question %}}

<!--more-->

## 简要做法

（这道题是从第 $0$ 天开始的

首先，我们要预处理出 $f(x)$ 表示花费 $x$ 块钱，能买到的最大生产力

这个东西用完全背包可以搞

然后我们定义 $g(i,x)$ 表示 $i$ 时刻，拥有 $x$ 块钱时，最大的生产力

那么对于 $i$ 时刻，我们拥有 $j$ 资源，我们花费了 $k$ 块钱造农民

$i + 1$ 时刻，我们拥有的资源就是 $i$ 时刻剩余的资源 + 新造的农民生产的资源 + 原来就有的农民

即 $w=(j-k)+f(k)+g(i,j)$

那么 $i + 1$ 时刻所能拥有的最大生产力 $g(i+1,w)=max(g(i+1,w),f(k)+g(i,j))$

~~这是什么神仙 DP~~

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

const int N = 1e2 + 5;
const int M = 1e3 + 5;

int n, m, t;
int f[N], g[N][M], a[N], b[N];

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
    n = read(), m = read(), t = read();
    if (m >= t)
        return puts("0"), 0;
    for (int i = 1; i <= n; i++)
        a[i] = read(), b[i] = read();
    memset(f, -1, sizeof f);
    f[0] = 0;
    for (int i = 1; i <= n; i++)
        for (int j = a[i]; j <= 1000; j++)
            if (f[j - a[i]] != -1)
                f[j] = std::max(f[j], f[j - a[i]] + b[i]);
    memset(g, -1, sizeof g);
    g[0][m] = 0;
    for (int i = 0; i <= 1000; i++)
    {
        if (g[i][t] != -1)
            return printf("%d", i), 0;
        for (int j = 0; j <= t; j++)
        {
            if (g[i][j] == -1)
                continue;
            for (int k = 0; k <= j; k++)
            {
                if (f[k] == -1)
                    continue;
                int w = j - k + f[k] + g[i][j];
                if (w >= t)
                    return printf("%d", i + 1), 0;
                g[i + 1][w] = std::max(g[i + 1][w], f[k] + g[i][j]);
            }
        }
    }
    return 0;
}
```
