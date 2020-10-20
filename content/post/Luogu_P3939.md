+++
title = 'Luogu P3939 数颜色（主席树）'
date = 2020-10-20T19:11:53+08:00
categories = ['题解']
tags = ['主席树']
+++

[题目链接](https://www.luogu.com.cn/problem/P3939)

{{% question %}}

$n$ 个点，$m$ 次操作,每个点有个颜色

两种操作

1. 查询给定区间内有多少个某个颜色的点
2. 交换两个点的颜色

$n \le 3 \times 10^5$

{{% /question %}}

<!--more-->

## 简要做法

每个颜色开个线段树，完事

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>

const int N = 3e5 + 5;

int n, q, col[N];

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

struct HJT_Tree
{
#define mid ((l + r) >> 1)
    int nodecnt, L[N << 5], R[N << 5], rt[N], sum[N << 5];
    void modify(int &p, int l, int r, int pos, int val)
    {
        if (!p)
            p = ++nodecnt;
        sum[p] += val;
        if (l == r)
            return;
        if (pos <= mid)
            modify(L[p], l, mid, pos, val);
        else
            modify(R[p], mid + 1, r, pos, val);
    }
    int query(int p, int l, int r, int x, int y)
    {
        if (!p)
            return 0;
        if (x <= l and r <= y)
            return sum[p];
        int ans = 0;
        if (x <= mid)
            ans += query(L[p], l, mid, x, y);
        if (mid < y)
            ans += query(R[p], mid + 1, r, x, y);
        return ans;
    }
} T;

int main()
{
    n = read(), q = read();
    for (int i = 1; i <= n; i++)
        col[i] = read(), T.modify(T.rt[col[i]], 1, n, i, 1);
    for (int opt, x, y; q--;)
    {
        opt = read(), x = read();
        if (opt == 1)
            y = read(), printf("%d\n", T.query(T.rt[read()], 1, n, x, y));
        else
        {
            T.modify(T.rt[col[x]], 1, n, x, -1), T.modify(T.rt[col[x + 1]], 1, n, x + 1, -1);
            T.modify(T.rt[col[x]], 1, n, x + 1, 1), T.modify(T.rt[col[x + 1]], 1, n, x, 1);
            std::swap(col[x], col[x + 1]);
        }
    }
    return 0;
}
```
