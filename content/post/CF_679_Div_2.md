+++
title = 'Codeforces Round #679 (Div. 2) 划水记录'
date = 2020-10-26T07:59:42+08:00
categories = ['比赛记录']
+++

[18:44] scy：“晚上七点 cf 打吗”

18:55 才看到消息，我草，赶紧打开 cf

竟然是 Chinese Friendly Round /cy

<!--more-->

# A

## 简要做法

头尾配对即可

## 参考代码

```cpp
#include <stdio.h>

const int N = 1e5 + 5;

int n, m, a[N];

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
    for (int T = read(); T--; puts(""))
    {
        n = read();
        for (int i = 1; i <= n; i++)
            a[i] = read();
        for (int i = 1; i <= n; i++)
            printf("%d ", (i <= n / 2 ? -1 : 1) * a[n - i + 1]);
    }
    return 0;
}
```

# B

## 简要做法

给定的两个矩阵相当于确定了每个数的横坐标和纵坐标

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>

const int N = 5e2 + 5;

int n, m;

struct Node
{
    int x, y, num;
    bool friend operator<(Node a, Node b) { return a.x == b.x ? a.y < b.y : a.x < b.x; }
} a[N * N];

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
    for (int T = read(); T--;)
    {
        n = read(), m = read();
        for (int i = 1; i <= n; i++)
            for (int j = 1, k; j <= m; j++)
                k = read(), a[k].y = j, a[k].num = k;
        for (int j = 1; j <= m; j++)
            for (int i = 1, k; i <= n; i++)
                k = read(), a[k].x = i;
        std::sort(a + 1, a + 1 + n * m);
        for (int k = 1; k <= n * m; k++)
        {
            printf("%d ", a[k].num);
            if (k % m == 0)
                puts("");
        }
    }
    return 0;
}

```

# C

## 简要做法

分享一个 Pecco 的文章

[在打CodeForces的过程中发现的一个小模型](https://zhuanlan.zhihu.com/p/268630329)

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>
#include <vector>

const int N = 1e5 + 5;
const int m = 6;
const int M = m + 5;
const int INF = 1 << 30;

int n;
int a[M], b[N], c[N][M];

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

int min(std::vector<std::pair<int, int>> &V)
{
    if (V.empty())
        return 0;
    std::sort(V.begin(), V.end());
    std::vector<std::pair<int, int>> U{V[V.size() - 1]};
    for (int i = V.size() - 1; i >= 0; i--)
        if (V[i].second > U[U.size() - 1].second)
            U.emplace_back(V[i]);
    int res = std::min(U[0].first, U[U.size() - 1].second);
    for (int i = 0; i < U.size() - 1; i++)
        res = std::min(res, U[i].second + U[i + 1].first);
    return res;
}

int go(int x)
{
    std::vector<std::pair<int, int>> V;
    for (int i = 2; i <= n; i++)
    {
        int l = INF, r = INF;
        for (int j = 1; j <= m; j++)
        {
            if (c[i][j] <= x)
                l = std::min(l, x - c[i][j]);
            if (c[i][j] >= x)
                r = std::min(r, c[i][j] - x);
        }
        V.emplace_back(l, r);
    }
    return min(V);
}

int main()
{
    for (int i = 1; i <= 6; i++)
        a[i] = read();
    n = read();
    for (int i = 1; i <= n; i++)
        b[i] = read();
    std::sort(a + 1, a + 1 + m);
    std::sort(b + 1, b + 1 + n);
    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            c[i][j] = b[i] - a[j];
    int ans = INF;
    if (n == 1)
        ans = 0;
    else
        for (int i = 1; i <= m; i++)
            ans = std::min(ans, go(c[1][i]));
    return printf("%d", ans), 0;
}
```

# D

## 简要做法

维护一个栈

贪心保持出栈单调

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <stack>

const int N = 2e5 + 5;

int n, top;
int ans[N];
std::stack<int> S;

struct Node
{
    int opt, val;
} a[N];

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
    for (int i = 1; i <= n << 1; i++)
    {
        char opt;
        scanf("%s", &opt);
        if (opt == '+')
            a[i].opt = 1;
        else
            a[i].val = read(), a[i].opt = 2;
    }
    n <<= 1;
    for (int i = 1; i <= n; i++)
        if (a[i].opt == 1)
            top++;
        else if (!top)
            return puts("NO"), 0;
        else
            top--;
    int now = n >> 1;
    for (int i = n; i; i--)
        if (a[i].opt == 1)
            ans[now--] = S.top(), S.pop();
        else if (!S.empty() and a[i].val > S.top())
            return puts("NO"), 0;
        else
            S.push(a[i].val);
    puts("YES");
    for (int i = 1; i <= n >> 1; i++)
        printf("%d ", ans[i]);
    return 0;
}
```

# E

## 简要做法

手玩

## 参考代码

```cpp
#include <stdio.h>

#define int long long

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
    for (int T = read(); T--;)
    {
        int x = 0, a = read(), b = read(), c = read(), d = read();
        if (b * c < a)
            puts("-1");
        else if (a % (b * d))
            x = a / (b * d);
        else
            x = a / (b * d) - 1;
        printf("%lld\n", (x + 1) * a - d * b * (x + 1) * x / 2);
    }
    return 0;
}
```