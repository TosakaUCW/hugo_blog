+++
title = "Luogu P1306 斐波那契公约数（数论，矩阵加速）"
categories = ["题解"]
tags = ["数论", "矩阵加速"]
date = "2019-01-16T23:25:30+08:00"
description = ""
aliases = ["/post/Luogu_P1306", "/Luogu_P1306"]
+++

[题目链接](https://www.luogu.org/problemnew/show/P1306)

{{% question %}}
给定正整数 \`n\` 和 \`m\`

求`Fibonacci`数列第 \`n\` 项和第 \`m\` 项的最大公因数

\`n,m \le 10^9\`
{{% /question %}}

<!--more-->

## 简要做法

### 结论

$$ gcd(F[n], F[m]) = F[gcd(n, m)] $$

### 证明

对于\`n<m\`，我们设\`F[n] = a\`，\`F[n+1] = b\`

则\`F[n+2] = a+b\`，\`F[n+3] = a+2 \times b\`

归纳一下得\`F[m] = F[m-n-1] \times a + F[m-n] \times b\`

所以\`gcd(F[n], F[m]) = gcd(F[n], F[m-n-1] \times a + F[m-n] \times b)\`

将\`F[n] = a\`，\`F[n+1] = b\`代入

得\`gcd(F[n], F[m]) = gcd(F[n], F[m-n-1] \times F[n] + F[m-n] \times F[n+1])\`

所以\`gcd(F[n], F[m]) = gcd(F[n], F[m-n] \times F[n+1])\`

因为\`gcd(F[n], F[n+1]) = 1\`

所以\`gcd(F[n], F[m]) = gcd(F[n], F[m-n])\`

所以\`gcd(F[n], F[m]) = gcd(F[n], F[m \bmod n])\`

所以\`gcd(F[n], F[m]) = F[gcd(n, m)]\`

## 参考代码

```c++
#include <memory.h>
#include <stdio.h>

typedef long long LL;

const int N = 2;
const int P = 1e8;

LL n, m;

struct Matrix
{
    LL a[N + 5][N + 5];
    Matrix() { memset(a, 0, sizeof(a)); }
    Matrix friend operator*(Matrix X, Matrix Y)
    {
        Matrix Z;
        for (int i = 1; i <= N; i++)
            for (int j = 1; j <= N; j++)
                for (int k = 1; k <= N; k++)
                    Z.a[i][j] = (Z.a[i][j] + X.a[i][k] * Y.a[k][j] % P) % P;
        return Z;
    }
} ans, base;

Matrix pow(Matrix base, LL k)
{
    while (k)
    {
        if (k & 1)
            ans = ans * base;
        base = base * base;
        k >>= 1;
    }
    return ans;
}

LL gcd(LL a, LL b)
{
    return b ? gcd(b, a % b) : a;
}

int main()
{
    scanf("%lld%lld", &n, &m);
    n = gcd(n, m);
    if (n <= 2)
    {
        printf("1");
        return 0;
    }
    ans.a[1][1] = ans.a[1][2] = 1;
    base.a[1][1] = base.a[1][2] = base.a[2][1] = 1;
    ans = pow(base, n - 2);
    printf("%lld", ans.a[1][1]);
    return 0;
}
```