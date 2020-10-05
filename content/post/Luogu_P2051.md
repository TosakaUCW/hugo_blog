+++
title = 'Luogu P2051 [AHOI2009]中国象棋（DP）'
date = 2020-10-05T21:04:17+08:00
categories = ['题解']
tags = ['DP']
+++

[题目链接](https://www.luogu.com.cn/problem/P2051)

{{% question %}}

给定一个 $n \times m$ 的棋盘，求放棋子使得每一行每一列棋子个数小于等于 $2$ 的方案总数

$1 \le n,m \le 100$

{{% /question %}}

<!--more-->

## 简要做法

状态比较关键，设 $f(i,j,k))$ 表示前 $i$ 行，有 $j$ 列存在一个棋子，有 $k$ 列存在二个棋子的方案数

当这一行不放棋子，方案为 $f(i-1,j,k)$

放在空列，放一个，方案为 $f(i-1,j-1,k) \times (m - (j - 1) - k)$

放在空列，放两个，方案为 $f(i-1,j-2,k) \times C_{m-(j-2)-k}^2$

放在已有一个的列，放一个，方案为 $f(i-1,j+1,k-1) \times (j+1)$

放在已有一个的列，放两个，方案为 $f(i-1,j+2,k-2)\times C_{j+2}^2$

在空列和已有一个的列各放一个，方案为 $f(i-1,j,k-1) \times j \times (m-j-(k-1))$

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>

#define int long long

const int N = 2e2 + 5;
const int magic = 4999987;
const int P = 9999973;

int n, m, ans;
int fac[N], inv[N];
int f[N][N][N];

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

int C(int n) { return n * (n - 1) % P * magic % P; }

signed main()
{
    n = read(), m = read(), f[0][0][0] = 1;
    for (int i = 1; i <= n; i++)
        for (int j = 0; j <= m; j++)
            for (int k = 0; k + j <= m; f[i][j][k++] %= P)
            {
                f[i][j][k] = f[i - 1][j][k];
                if (k >= 1)
                    f[i][j][k] += f[i - 1][j + 1][k - 1] * (j + 1) % P,
                        f[i][j][k] += f[i - 1][j][k - 1] * j * (m - j - k + 1) % P;
                if (k >= 2)
                    f[i][j][k] += f[i - 1][j + 2][k - 2] * C(j + 2) % P;
                if (j >= 1)
                    f[i][j][k] += f[i - 1][j - 1][k] * (m - j - k + 1) % P;
                if (j >= 2)
                    f[i][j][k] += f[i - 1][j - 2][k] * C(m - j - k + 2) % P;
            }
    for (int i = 0; i <= m; i++)
        for (int j = 0; j <= m; j++)
            (ans += f[n][i][j]) %= P;
    return printf("%lld", (ans + P) % P), 0;
}
```
