+++
title = 'Luogu P4329 [COCI2006-2007#1] Bond（状态压缩，DP）'
date = 2020-09-23T19:54:08+08:00
categories = ['题解']
tags = ['状态压缩', 'DP']
+++

[题目链接](https://www.luogu.com.cn/problem/P4329)

{{% question %}}

有 $n$ 个人去执行 $n$ 个任务，每个人执行每个任务有不同的成功率，每个人只能执行一个任务，求所有任务都执行的总的成功率。

$1 \le n \le 20$

{{% /question %}}

<!--more-->

## 简要做法

明示状压

但是这道题，如果设两个状态分别为人和任务的话，显然是不行的

实际上，人和任务可以有一个是固定的

可以只设人的状态，通过人的状态搞下 lowbit 就知道现在即将做第几个任务，枚举转移即可

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

const int N = 20 + 1;

int n;
double c[N][N], f[1 << N];

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
    n = read();
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= n; j++)
            c[i][j] = read() / 100.0;
    f[0] = 1;
    for (int i = 0; i < (1 << n); i++)
    {
        int k = 1;
        for (int tmp = i; tmp; tmp -= tmp & (-tmp))
            k++;
        for (int j = 1; j <= n; j++)
            if (!(i & (1 << (j - 1))))
                f[i | (1 << (j - 1))] = std::max(f[i | (1 << (j - 1))], f[i] * c[j][k]);
    }
    printf("%f", f[(1 << n) - 1] * 100);
    return 0;
}
```
