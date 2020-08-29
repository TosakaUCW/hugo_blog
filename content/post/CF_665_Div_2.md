+++
title = 'Codeforces Round #665 (Div. 2) 划水记录'
date = 2020-08-29T08:19:42+08:00
categories = ['比赛记录']
+++

暑假最后一场 CF，我将直冲 newbie，更下一层楼

C D 乱杀，A B 卡一万年，果然人傻不适合打 CF

<!--more-->

# A

{{% question %}}

在数轴的正半轴上有一个点 $A$，其坐标为 $n$，求最少将它移动多少个单位长度使得存在点 $B$ 满足 $\left| \ \left| BO \right|  \  -  \  \left| AB\right| \ \right| = k$

{{% /question %}}

## 简要做法

当 k > n$

把 $A$ 移到 $k$ 的位置，然后 $B$ 在 $O$ 点即可满足条件

答案为 $k - n$

当 $k \le n$

$B$ 就在 $OA$ 那一段里面

yy 一下判断下奇偶性即可

## 参考代码

```cpp
#include <stdio.h>

typedef long long ll;

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
        ll n = read(), k = read();
        if (k >= n)
            printf("%lld\n", k - n);
        else
            printf("%lld\n", (n - k) % 2);
    }
    return 0;
}
```

# B

{{% question %}}

两个序列 A,B。这两个序列都是由 $0,1,2$ 这三个数构成。我们给出  $0,1,2$ 这三个数在 A 序列中的数量$x_1,y_1,z_1$ 和 $0,1,2$ 这三个数在 B 序列中的数量 $x_2,y_2,z_2$。($x_1+y_1+z_1 = x_2+y_2+z_2  $)

你可以重新排列两个序列中的元素，然后生成一个新序列 C，C 的生成规则：懒得打 $\LaTeX$ 了，自己去原题看

你需要找到 $\sum^n_{i=1}C_i$ 的最大值。

**多组数据**，$0 \le x_1,x_2,y_1,y_2,z_1,z_2 \le 10^8$。

{{% /question %}}

## 简要做法

当且仅当 $2 \times 1$ 会产生正收益

当且仅当 $1 \times 2$ 会产生负收益

贪心即可

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>

int A[3], B[3];

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
        for (int i = 0; i < 3; i++)
            A[i] = read();
        for (int i = 0; i < 3; i++)
            B[i] = read();
        int tmp = std::min(A[0], B[2]);
        A[0] -= tmp, B[2] -= tmp;
        tmp = std::min(A[2], B[2]);
        A[2] -= tmp, B[2] -= tmp;
        printf("%d\n", (std::min(A[2], B[1]) - std::min(A[1], B[2])) * 2);
    }
    return 0;
}
```

# C

{{% question %}}

有一列正整数：$a_1,a_2,\dots,a_n$，每次操作，你可以选择 $1\le i,j\le n$，如果 $\gcd(a_i,a_j)$ 等于整个数组中最小的元素，你就可以交换 $a_i$ 和 $a_j$，其中 $\gcd(x,y)$ 表示 $x$ 和 $y$ 的最大公因数。

你可以进行任意数量的操作，请判断能否通过这些操作使得 $a$ 变为不下降序列。

{{% /question %}}

## 简要做法

倒推

把目标状态搞出来，看看目标状态有没有办法回到起始状态

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>

const int N = 1e5 + 5;

int n;

struct Node
{
    int val, pos;
    bool friend operator<(Node a, Node b) { return a.val < b.val; }
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
    for (int T = read(); T--;)
    {
        bool flag = true;
        n = read();
        for (int i = 1; i <= n; i++)
            a[i].val = read(), a[i].pos = i;
        std::stable_sort(a + 1, a + 1 + n);
        for (int i = 1; i <= n; i++)
            if (a[i].pos != i and a[i].val % a[1].val != 0)
            {
                flag = false;
                break;
            }
        if (flag)
            puts("YES");
        else
            puts("NO");
    }
    return 0;
}
```

# D

{{% question %}}

给定一棵 $n$ 个节点，$n-1$ 条边的树。你可以在每一条树上的边标上边权，使得：

1. 每个边权都为 **正整数**；
2. 这 $n-1$ 个边权的 **乘积** 等于 $k$；
3. 边权为 $1$ 的边的数量最少。

定义 $f(u,v)$ 表示节点 $u$ 到节点 $v$ 的简单路径经过的边权总和。你的任务是让 $\sum\limits_{i=1}^{n-1}\sum\limits_{j=i+1}^{n} f(i,j)$ 最大。

最终答案可能很大，对 $10^9+7$ 取模即可。

$k$ 有可能很大，输入数据中包含了 $m$ 个质数 $p_i$，那么 $k$ 为这些质数的乘积。

{{% /question %}}

## 简要做法

贪心水题

统计每条边的贡献暴力计算即可

## 参考代码

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

typedef long long ll;

const ll N = 1e6 + 5;
const ll M = 2e6 + 5;
const ll P = 1e9 + 7;

ll n, m, ans;
ll p[N], size[N], tot[N];
ll head[N], num_edge;

struct Node
{
    ll next, to;
} edge[M];

void add_edge(ll u, ll v) { edge[++num_edge] = Node{head[u], v}, head[u] = num_edge; }

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

void dfs(ll u, ll fa)
{
    size[u] = 1;
    for (ll i = head[u], v; i; i = edge[i].next)
        if ((v = edge[i].to) != fa)
            dfs(v, u), size[u] += size[v];
    tot[u] = size[u] * (n - size[u]);
}

signed main()
{
    for (ll T = read(); T--; num_edge = ans = 0, memset(head, 0, sizeof head))
    {
        n = read();
        for (ll i = 1, u, v; i < n; i++)
            u = read(), v = read(), add_edge(u, v), add_edge(v, u);
        m = read();
        for (ll i = 1; i <= m; i++)
            p[i] = read();
        dfs(1, 0);
        for (ll i = m + 1; i < n; i++)
            p[i] = 1;
        std::sort(p + 1, p + 1 + std::max(n - 1, m));
        for (ll i = n; i <= m; i++)
            p[n - 1] = p[n - 1] * p[i] % P;
        std::sort(tot + 2, tot + 1 + n);
        for (ll i = n - 1; i >= 1; i--)
            ans = (ans + p[i] % P * tot[i + 1] % P) % P;
        printf("%lld\n", (ans + P) % P);
    }
    return 0;
}
```