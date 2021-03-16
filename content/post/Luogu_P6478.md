+++
title = 'Luogu P6478 [NOI Online #2 提高组] 游戏(二项式反演，树形DP）'
date = 2021-03-16T18:19:45+08:00
categories = ['题解']
tags = ['二项式反演', 'DP', '树形DP']
+++

[题目链接](https://www.luogu.com.cn/problem/P6478)

{{% question %}}

给定一棵 $n=2m$ 个节点的树，有 $m$ 个白点，$m$ 个黑点

把黑白点两两配对，对于 $k\in[0,n]$，求出恰好有 $k$ 对点有祖孙关系的方案数

$n \le 5*10^3$

{{% /question %}}

<!--more-->

## 简要做法

$f(i)$ 恰好，$g(i)$ 钦定

$$g(k)=\sum_{i=k}^{m}{i \choose k}f(i)$$

$$f(k) = \sum_{i = k} ^ {m} (-1) ^ {i - k} {i \choose k} g(i)$$

树形 DP 求 $g(i)$

$dp(u,i)$ 表示 $u$ 的子树内，选出恰好 $i$ 组有祖孙关系的点对的方案数

假若不选择 $u$，合并子节点即可

假若选择 $u$，则 $dp(u,i+1) += dp(u,i) \times 颜色不同且未配对的点的数量$

$g(k) = dp(1,k) \times (m-k)!$

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

#define int long long

int read(int x = 0, int f = 0, char ch = getchar())
{
    while ('0' > ch or ch > '9')
        f = ch == '-', ch = getchar();
    while ('0' <= ch and ch <= '9')
        x = x * 10 + (ch ^ 48), ch = getchar();
    return f ? -x : x;
}

const int N = 5e3 + 5;
const int M = N << 1;
const int P = 998244353;

int n, ans;
int a[N];
int head[N], num_edge;
int fac[N], inv[N];
int f[N][N], g[N], siz1[N], siz2[N];

struct Node
{
    int next, to;
} edge[M];

void add_edge(int u, int v) { edge[++num_edge] = Node{head[u], v}, head[u] = num_edge; }

int pow(int x, int k, int res = 1)
{
    for (x %= P; k; x = x * x % P, k >>= 1)
        if (k & 1)
            res = res * x % P;
    return res;
}

int C(int i, int j) { return fac[i] * inv[j] % P * inv[i - j] % P; }

void dfs(int u, int fa)
{
    siz1[u] = f[u][0] = 1, siz2[u] = a[u];
    for (int i = head[u], v; i; i = edge[i].next)
        if ((v = edge[i].to) != fa)
        {
            dfs(v, u);
            for (int i = 0; i <= siz1[u] + siz1[v]; i++)
                g[i] = 0;
            for (int i = 0; i <= std::min(siz1[u], n / 2); i++)
                for (int j = 0; j <= std::min(siz1[v], n / 2 - i); j++)
                    (g[i + j] += f[u][i] * f[v][j] % P) %= P;
            for (int i = 0; i <= siz1[u] + siz1[v]; i++)
                f[u][i] = g[i];
            siz1[u] += siz1[v], siz2[u] += siz2[v];
        }
    for (int i = std::min(siz2[u], siz1[u] - siz2[u]); i; i--)
        if (a[u])
            (f[u][i] += f[u][i - 1] * (siz1[u] - siz2[u] - (i - 1)) % P) %= P;
        else
            (f[u][i] += f[u][i - 1] * (siz2[u] - (i - 1)) % P) %= P;
}

signed main()
{
    n = read(), fac[0] = 1;
    for (int i = 1; i <= n; i++)
        scanf("%1lld", &a[i]);
    for (int i = 1; i <= n; i++)
        fac[i] = fac[i - 1] * i % P;
    inv[n] = pow(fac[n], P - 2);
    for (int i = n; i >= 1; i--)
        inv[i - 1] = inv[i] * i % P;
    for (int i = 1, u, v; i < n; i++)
        u = read(), v = read(), add_edge(u, v), add_edge(v, u);
    dfs(1, 0);
    for (int i = 0; i <= n / 2; i++)
        (f[1][i] *= fac[n / 2 - i]) %= P;
    for (int i = 0; i <= n / 2; printf("%lld\n", ans), ans = 0, i++)
        for (int j = i; j <= n / 2; j++)
            if ((j - i) & 1)
                (ans += -C(j, i) * f[1][j] % P + P) %= P;
            else
                (ans += C(j, i) * f[1][j] % P) %= P;
    return 0;
}
```
