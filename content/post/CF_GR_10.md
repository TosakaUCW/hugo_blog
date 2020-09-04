+++
title = 'Codeforces Global Round 10 划水记录'
date = 2020-08-28T08:19:42+08:00
categories = ['比赛记录']
+++

**草，weak pretest，我日他大坝**

<div align=center>

![](https://i.loli.net/2020/09/02/lbP4LAITBzm7Uit.png)

</div>

<!--more-->

# A

{{% question %}}

给定一个数列，当 $a_i \neq a_{i+1}$ 时，可以合并 $a_i$ 和 $a_{i+1}$，求若干次操作之后的数列的最短长度

{{% /question %}}

## 简要做法

考虑数列中的最大值 $x$，必然能和某个小的数合成一个更大的数，如此下去最终只剩一个

但是如果全都是同一个数，就不能这样合并，答案就为原序列长度

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

const int N = 2e5 + 5;

int n, a[N];

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
        int n = read();
        bool flag = false;
        for (int i = 1; i <= n; i++)
        {
            a[i] = read();
            if (a[i] != a[i - 1] and i != 1)
                flag = true;
        }
        printf("%d\n", flag ? 1 : n);
    }
    return 0;
}
```

# B

{{% question %}}

给你一个数列 $a$，然后每次操作选出最大值 $d$，对于 $1\le i\le n$，把 $a_i$ 换成 $d-a_i$。把这个操作进行 $k$ 次，输出最后的数列。

{{% /question %}}

## 简要做法

手玩一下样例就知道了，最终答案只会在两种序列中来回变换，判断一下 $k$ 的奇偶性即可

~~pretest 真 nm weak，连 long long 的数据都没有，然后只有 k 要开 long long，cf 我日你大坝~~

周巨 快读 + k & 1 在负数的情况下判断奇偶性也不会炸，而用取模会因为负数的原因炸了，奇妙的技巧增加了.jpg

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

typedef long long ll;

const ll N = 2e5 + 5;
const ll INF = 2147483647;

ll n, k;
ll a[N], b[N], ans[N];

ll read()
{
    ll x = 0, f = 1;
    char ch = getchar();
    while ('0' > ch or ch > '9')
        f = ch == '-' ? -1 : 1, ch = getchar();
    while ('0' <= ch and ch <= '9')
        x = x * 10 + ch - 48, ch = getchar();
    return x * f;
}

signed main()
{
    for (ll T = read(); T--;)
    {
        ll d1 = -INF, d2 = -INF;
        n = read(), k = read();
        for (ll i = 1; i <= n; i++)
            a[i] = read(), d1 = std::max(d1, a[i]);
        for (ll i = 1; i <= n; i++)
            a[i] = d1 - a[i];
        for (ll i = 1; i <= n; i++)
            d2 = std::max(d2, a[i]);
        for (ll i = 1; i <= n; i++)
            b[i] = d2 - a[i];
        for (ll i = 1; i <= n; i++)
            printf("%lld ", k % 2 ? a[i] : b[i]);
        puts("");
    }
    return 0;
}
```

# C

{{% question %}}

给定一个序列，定义一次操作为将一段数全部加 $1$，问将序列变为非严格递增的最小次数。

{{% /question %}}

## 简要做法

贪心

线性地考虑这个序列，假设我们现在 $[1,i-1]$ 这一段已经非严格递增了，考虑 $i$ 这个位置

假如 $a_{i-1} \leq a_i$，那么 $[1,i]$ 这一段也符合要求了，继续考虑下一位

否则就将 $[1,i-1]$ 这一段不断增加，使得序列符合要求

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

typedef long long ll;

const ll N = 2e5 + 5;
const ll INF = 2147483647;

ll n, ans;
ll a[N], g[N];

ll read()
{
    ll x = 0, f = 1;
    char ch = getchar();
    while ('0' > ch or ch > '9')
        f = ch == '-' ? -1 : 1, ch = getchar();
    while ('0' <= ch and ch <= '9')
        x = x * 10 + ch - 48, ch = getchar();
    return x * f;
}

signed main()
{
    for (ll T = read(); T--;)
    {
        n = read(), ans = 0;
        for (int i = 1; i <= n; i++)
            a[i] = read();
        for (int i = 2; i <= n; i++)
            if (a[i] < a[i - 1])
                ans += a[i - 1] - a[i];
        printf("%lld\n", ans);
    }
    return 0;
}
```

# D

{{% question %}}

$n$ 个人站成一个环，每个人会攻击左边或右边的人

定义合法状态：

1. 如果 $a$ 在攻击 $b$，且 $b$ 只受到 $a$ 的攻击时，$b$ 必须攻击 $a$。
2. 如果 $a$ 被旁边两人同时攻击或旁边两人都不攻击他，那么他可以随意选择一个人攻击。

求至少需要改变几个人的攻击方向才能使状态合法

{{% /question %}}

## 简要做法

一个合法当且仅当不存在 $3$ 个连续的 $L$ 或 $R$

注意特判全 $L$ 或 $R$ 的状态即可

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>
#include <string>
#include <iostream>
#include <math.h>

const int N = 4e5 + 5;

int n;
int a[N];
std::string str;

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
        memset(a, 0, sizeof a);
        str.clear();
        std::cin >> n >> str;
        for (int i = 0; i < n; i++)
            a[i + 1] = str[i];
        bool flag = false;
        for (int i = 1; i <= n; i++)
            if (i != 1 and a[i] != a[i - 1])
            {
                flag = true;
                break;
            }
        if (!flag)
            printf("%d\n", int(ceil(1.0 * n / 3)));
        else
        {
            int cnt = 0, ans = 0;
            int p, q;
            for (p = 1; a[p] == a[n]; p++)
                ;
            for (q = n; a[q] == a[1]; q--)
                ;
            ans += (n - q - 1 + p) / 3;
            cnt = 0;
            for (int i = p; i <= q; i++)
                if (a[i] != a[i - 1])
                    ans += cnt / 3, cnt = 1;
                else
                    cnt++;
            ans += cnt / 3;
            printf("%d\n", ans);
        }
    }
    return 0;
}
```