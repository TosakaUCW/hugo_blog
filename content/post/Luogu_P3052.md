+++
title = 'Luogu P3052 [USACO12MAR]Cows in a Skyscraper G（状态压缩，DP）'
date = 2020-09-24T20:17:56+08:00
categories = ['题解']
tags = ['状态压缩', 'DP']
+++

[题目链接](https://www.luogu.com.cn/problem/P3052)

{{% question %}}

给出 $n$ 个物品，体积为 $w_i$，现把其分成若干组，要求每组总体积$\le m$，问最小分组。

$1 \le n \le 18$

{{% /question %}}

<!--more-->

## 简要做法

定义 $f(k,i)$ 代表分了 $k$ 组，状态为 $i$，第 $k$ 组的总体积的最小值

那么对于当前这个物品可以更新当前这一组（如果容量不爆的话），和新开一组

统计答案时扫最小的 $i$ 使得 $f(i,target_status)$ 有解即可

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

const int N = 18 + 1;
const int INF = 0x3f3f3f3f;

int n, m, sta;
int a[N], f[N][1 << N];

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
    memset(f, 0x3f, sizeof f);
    n = read(), m = read(), sta = (1 << n) - 1;
    for (int i = 0; i < n; i++)
        f[1][1 << i] = a[i] = read();
    for (int k = 0; k <= n; k++)
        for (int i = 0; i <= sta; i++)
            if (f[k][i] != INF)
                for (int j = 0; j < n; j++)
                {
                    if (i & (1 << j))
                        continue;
                    if (f[k][i] + a[j] <= m)
                        f[k][i | (1 << j)] = std::min(f[k][i | (1 << j)], f[k][i] + a[j]);
                    else
                        f[k + 1][i | (1 << j)] = std::min(f[k][i | (1 << j)], a[j]);
                }
    for (int i = 0; i <= n; i++)
        if (f[i][sta] != INF)
        {
            printf("%d", i);
            break;
        }
    return 0;
}
```
