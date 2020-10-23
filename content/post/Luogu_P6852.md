+++
title = 'Luogu P6852 Mex（思维）'
date = 2020-10-23T08:27:17+08:00
categories = ['题解']
tags = ['思维']
+++

[题目链接](https://www.luogu.com.cn/problem/P6852)

{{% question %}}

给定 $n$ 和 $m$ 条 区间 mex 信息

要求构造一个 $0$ 到 $n$ 的合法排列

{{% /question %}}

<!--more-->

## 简要做法

$mex(l,r) = k$，就是说 区间 $[l,r]$ 内出现过 $[0,k-1]$，没出现过 $k$

那么对于一个 $k$，$k$ 一定要出现的区间即为 $mex > k$ 的区间的交集，$k$ 不能出现的区间即为 $mex = k$ 的区间的并集

考虑从小到大填数，这样一定出现的区间的是不变或者不断拓展的，那么填的时候随便填就可以了

那么可以用一个 `set` 维护可以放置的位置，每次二分查找即可

$mex = 0$ 的情况特判即可

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>
#include <set>

const int N = 5e5 + 5;

int n, m;
int al[N], ar[N], bl[N], br[N], c[N], ans[N];
std::set<int> S;

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

// a[k] : mex > k 的区间交
// b[k] : mex = k 的区间并

int main()
{
    n = read();
    for (int i = 0; i <= n; i++)
        al[i] = 0, ar[i] = n, bl[i] = n + 1, br[i] = -1, S.insert(i);
    for (int m = read(); m--;)
    {
        int l = read(), r = read(), k = read();
        if (k)
            al[k - 1] = std::max(al[k - 1], l), ar[k - 1] = std::min(ar[k - 1], r),
                   bl[k] = std::min(bl[k], l), br[k] = std::max(br[k], r);
        else
            ++c[l], --c[r + 1];
    }
    for (int i = n - 1; i >= 0; i--)
        al[i] = std::max(al[i], al[i + 1]),
        ar[i] = std::min(ar[i], ar[i + 1]);
    for (int i = 0; i <= n; i++)
    {
        if (i != 0)
            c[i] += c[i - 1];
        if (al[0] <= i and i <= ar[0] and c[i] == 0)
        {
            ans[i] = 0, S.erase(i);
            break;
        }
    }
    for (int i = 1; i <= n; i++)
    {
        std::set<int>::iterator it = S.lower_bound(al[i]);
        if (it != S.end() and *it <= ar[i] and (*it < bl[i] or br[i] < *it))
            ans[*it] = i, S.erase(it);
        else if (br[i] != -1)
        {
            it = S.lower_bound(br[i] + 1);
            if (it != S.end() and *it <= ar[i])
                ans[*it] = i, S.erase(it);
        }
    }
    if (!S.empty())
        return puts("-1"), 0;
    for (int i = 0; i <= n; i++)
        printf("%d ", ans[i]);
    return 0;
}
```
