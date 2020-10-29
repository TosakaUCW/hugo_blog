+++
title = 'Codeforces Round #162 (Div. 1) VP 记录'
date = 2020-10-29T20:20:09+08:00
categories = ['练习记录']
+++

[题目链接](https://codeforces.com/contest/264)

无内鬼，整点 VP

<!--more-->

# A

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>
#include <string>
#include <iostream>

int len;
std::string str;

void dfs(int x)
{
    if (x == len)
        return;
    if (str[x] == 'l')
        dfs(x + 1), printf("%d\n", x + 1);
    else
        printf("%d\n", x + 1), dfs(x + 1);
}

int main()
{
    std::cin >> str;
    return len = str.size(), dfs(0), 0;
}
```

# B

{{% question %}}

给定一个严格上升的子序列，求其相邻元素不互质的最长上升子序列长度

$n,a_i \le 10^5$

{{% /question %}}

## 简要做法

$$ f_i = \max_{\gcd(a_j,a_i)>1\ ,\ j < i}{f_j} + 1 $$

暴力的 $O(n^2)$ 是显然的，考虑优化状态转移

$f_i$ 能从 $f_j$ 转移过来，当且仅当 $a_i$ 和 $a_j$ 有共同因子

记录一个数组 $g_i = \max{f_x}$，其中 $i | x$

对于每个 $a_i$，将其分解质因数，从每个因子的 $g$ 那边转移过来，即每个因子能转移到的最大的 $f$

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <math.h>

const int N = 1e5 + 5;

int n, ans;
int a[N], b[N];
int f[N], g[N];
int cnt, prime[N];
bool isPrime[N];

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
        a[i] = read();
    for (int i = 1, tmp; i <= n; i++)
    {
        f[i] = 1, tmp = a[i];
        for (int j = 2; j * j <= a[i]; j++)
            if (tmp % j == 0)
            {
                f[i] = std::max(f[i], g[j] + 1);
                while (tmp % j == 0)
                    tmp /= j;
            }
        if (tmp > 1)
            f[i] = std::max(f[i], g[tmp] + 1);
        ans = std::max(ans, f[i]), tmp = a[i];
        for (int j = 2; j * j <= a[i]; j++)
            if (tmp % j == 0)
            {
                g[j] = std::max(g[j], f[i]);
                while (tmp % j == 0)
                    tmp /= j;
            }
        if (tmp > 1)
            g[tmp] = std::max(g[tmp], f[i]);
    }
    return printf("%d", ans), 0;
}
```

# C

{{% question %}}

有一个序列，每个元素有一个颜色 $c_i$ 和权值 $v_i$，你需要从中选出一些元素，不改变相对顺序的组成一个新序列，使得整个序列的总分最高。

新序列的分数是这样计算的：

对于每个你选出的元素：

如果这个元素和新序列中上一个元素颜色相同，就在总分里加上 $v_i \cdot a$，否则加上 $v_i \cdot b$

$n \times q \le 5 \times 10^7$

{{% /question %}}

## 简要做法

记录下最大值和次大值的颜色，每次转移的时候讨论下即可

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

#define int long long

const int N = 1e5 + 5;

int n, q;
int v[N], c[N], f[N];

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

signed main()
{
    n = read(), q = read();
    for (int i = 1; i <= n; i++)
        v[i] = read();
    for (int i = 1; i <= n; i++)
        c[i] = read();
    for (; q--;)
    {
        memset(f, 0xcf, sizeof f);
        int a = read(), b = read(), fir_col = 0, sec_col = 0, ans = 0;
        for (int i = 1; i <= n; i++)
        {
            int res = std::max(b * v[i], f[c[i]] + a * v[i]);
            if (c[i] != fir_col)
                res = std::max(res, f[fir_col] + b * v[i]);
            else
                res = std::max(res, f[sec_col] + b * v[i]);
            if (res > f[fir_col] and c[i] != fir_col)
                sec_col = fir_col, fir_col = c[i];
            else if (res > f[sec_col] and c[i] != fir_col)
                sec_col = c[i];
            f[c[i]] = std::max(f[c[i]], res);
            ans = std::max(ans, res);
        }
        printf("%I64d\n", ans);
    }
    return 0;
}
```

# D

鸽了

{{% question %}}

{{% /question %}}

## 简要做法

## 参考代码

```cpp
```