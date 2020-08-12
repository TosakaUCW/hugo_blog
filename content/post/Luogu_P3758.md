+++
title = 'Luogu P3758 [TJOI2017]可乐（邻接矩阵，矩阵乘法）'
date = 2020-08-13T03:23:38+08:00
categories = ['题解']
tags = ['邻接矩阵', '矩阵乘法']
+++

[题目链接](https://www.luogu.com.cn/problem/P3758)

{{% question %}}
给定一张图，对于所有的 i 和 j , 求从 i 到 j 恰好经过 k 步的总方案数
{{% /question %}}

<!--more-->

## 简要做法

可以假设我们现在求出了 $1$ 步的总方案数，用矩阵 $A_{i,j}$ 表示 从 $i$ 到 $j$ 恰好经过 $1$ 步的方案数

可以发现

$$A^2_{i,j} = \sum{A_{i,k} \times A_{k,j}}$$

$A^2$ 就是走 $2$ 步的方案数矩阵

发现这就是个矩阵乘法，所以直接矩阵快速幂算邻接矩阵 $k$ 次方即可

这道题有两种额外的操作

1. 留在原地
2. 走到一半自爆

第一种操作当成自环处理即可

第二种操作可以建个虚拟点拉个单向边，这样去了就再也回不来了

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

const int N = 1e5 + 5;
const int K = 30;
const int P = 2017;

int n, m, k, ans;

struct Matrix
{
    int a[K + 5][K + 5];
    Matrix() { memset(a, 0, sizeof a); }
    Matrix friend operator*(Matrix A, Matrix B)
    {
        Matrix C;
        for (int i = 0; i <= K; i++)
            for (int k = 0; k <= K; k++)
                for (int j = 0; j <= K; j++)
                    C.a[i][j] = (C.a[i][j] + A.a[i][k] * B.a[k][j] % P) % P;
        return C;
    }
} M, ansM;

Matrix pow(Matrix A, int k)
{
    Matrix C;
    for (int i = 0; i <= K; i++)
        C.a[i][i] = 1;
    while (k)
    {
        if (k & 1)
            C = C * A;
        A = A * A;
        k >>= 1;
    }
    return C;
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

int main()
{
    n = read(), m = read();
    for (int i = 1, u, v; i <= m; i++)
        u = read(), v = read(), M.a[u][v] = M.a[v][u] = 1;
    for (int i = 0; i <= n; i++)
        M.a[i][i] = 1;
    for (int i = 1; i <= n; i++)
        M.a[i][0] = 1;
    k = read();
    ansM = pow(M, k);
    for (int i = 0; i <= n; i++)
        ans = (ans + ansM.a[1][i]) % P;
    printf("%d", ans);
    return 0;
}
```
