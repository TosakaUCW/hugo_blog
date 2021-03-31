+++
title = 'LCT 入门实录'
date = 2021-03-31T20:20:05+08:00
categories = ['算法']
tags = ['LCT']
+++

菜鸡做题实录

参考文献

[OI-wiki](https://oi-wiki.org/ds/lct)

[FlashHu](https://www.cnblogs.com/flashhu/p/8324551.html)

[FlashHu](https://www.cnblogs.com/flashhu/p/9498517.html)

[ZZD](https://www.cnblogs.com/zhouzhendong/p/LCT.html)

Yang Zhe《SPOJ375 QTREE 解法的一些研究》

<!--more-->

### [BZOJ 1180] OTOCI

迫真板子题，也就图一乐

{{% fold "参考代码" %}}
```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

using std::swap;

int read(int x = 0, int f = 0, char ch = getchar())
{
    while ('0' > ch or ch > '9')
        f = ch == '-', ch = getchar();
    while ('0' <= ch and ch <= '9')
        x = x * 10 + (ch ^ 48), ch = getchar();
    return f ? -x : x;
}

const int N = 3e4 + 5;

int n, val[N];
int ch[N][2], f[N], sum[N], tag[N];

#define ls (ch[p][0])
#define rs (ch[p][1])
#define get(x) (ch[f[x]][1] == x)
#define isRoot(p) (ch[f[p]][0] != p and ch[f[p]][1] != p)
void pushDown(int p)
{
    if (tag[p])
    {
        swap(ch[ls][0], ch[ls][1]);
        swap(ch[rs][0], ch[rs][1]);
        tag[ls] ^= 1, tag[rs] ^= 1, tag[p] ^= 1;
    }
}
void update(int p)
{
    if (!isRoot(p))
        update(f[p]);
    pushDown(p);
}
void pushUp(int p) { sum[p] = sum[ls] + sum[rs] + val[p]; }
void Rotate(int x)
{
    int y = f[x], z = f[y], k = get(x);
    if (!isRoot(y))
        ch[z][ch[z][1] == y] = x;
    ch[y][k] = ch[x][!k], f[ch[y][k]] = y;
    ch[x][!k] = y, f[y] = x, f[x] = z;
    pushUp(y), pushUp(x);
}
void Splay(int x)
{
    update(x);
    for (int fa; fa = f[x], !isRoot(x); Rotate(x))
        if (!isRoot(fa))
            Rotate(get(x) == get(fa) ? fa : x);
}
int Access(int x, int p = 0)
{
    for (p = 0; x; p = x, x = f[x])
        Splay(x), ch[x][1] = p, pushUp(x);
    return p;
}
void makeRoot(int p) { p = Access(p), swap(ch[p][0], ch[p][1]), tag[p] ^= 1; }
void Link(int x, int p) { makeRoot(x), Splay(x), f[x] = p; }
void Split(int x, int p) { makeRoot(x), Access(p), Splay(p); }
void Cut(int x, int p) { Split(x, p), ls = f[x] = 0; }
int Find(int p)
{
    Access(p), Splay(p);
    while (ls)
        pushDown(p), p = ls;
    Splay(p);
    return p;
}

int main()
{
    n = read();
    for (int i = 1; i <= n; i++)
        val[i] = read();
    for (int q = read(); q--;)
    {
        static char opt[20];
        scanf("%s", opt);
        int x = read(), y = read();
        if (opt[0] == 'b')
        {
            int dx = Find(x), dy = Find(y);
            if (dx != dy)
                puts("yes"), Link(x, y);
            else
                puts("no");
        }
        else if (opt[0] == 'p')
            Split(x, x), val[x] = sum[x] = y;
        else
        {
            int dx = Find(x), dy = Find(y);
            if (dx != dy)
                puts("impossible");
            else
                Split(x, y), printf("%d\n", sum[y]);
        }
    }
    return 0;
}
```
{{% /fold %}}

### [Luogu P3690] 【模板】Link Cut Tree （动态树）

真·板子，注意 cut 写法

{{% fold "参考代码" %}}
```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>

using std::swap;

int read(int x = 0, int f = 0, char ch = getchar())
{
    while ('0' > ch or ch > '9')
        f = ch == '-', ch = getchar();
    while ('0' <= ch and ch <= '9')
        x = x * 10 + (ch ^ 48), ch = getchar();
    return f ? -x : x;
}

const int N = 1e5 + 5;

int n, q, val[N];
int ch[N][2], f[N], sum[N], tag[N];

#define ls ch[p][0]
#define rs ch[p][1]
#define get(x) (ch[f[x]][1] == x)
#define isRoot(x) (ch[f[x]][0] != x and ch[f[x]][1] != x)
void pushDown(int p)
{
    if (tag[p])
    {
        swap(ch[ls][0], ch[ls][1]);
        swap(ch[rs][0], ch[rs][1]);
        tag[ls] ^= 1, tag[rs] ^= 1, tag[p] ^= 1;
    }
}
void Update(int p)
{
    if (!isRoot(p))
        Update(f[p]);
    pushDown(p);
}
void pushUp(int p) { sum[p] = sum[ls] ^ sum[rs] ^ val[p]; }
void Rotate(int x)
{
    int y = f[x], z = f[y], k = get(x);
    if (!isRoot(y))
        ch[z][ch[z][1] == y] = x;
    ch[y][k] = ch[x][!k], f[ch[y][k]] = y;
    ch[x][!k] = y, f[y] = x, f[x] = z;
    pushUp(y), pushUp(x);
}
void Splay(int x)
{
    Update(x);
    for (int fa; fa = f[x], !isRoot(x); Rotate(x))
        if (!isRoot(fa))
            Rotate(get(x) == get(fa) ? fa : x);
}
int Access(int x, int p = 0)
{
    for (p = 0; x; p = x, x = f[x])
        Splay(x), ch[x][1] = p, pushUp(x);
    return p;
}
void makeRoot(int p) { p = Access(p), swap(ch[p][0], ch[p][1]), tag[p] ^= 1; }
void Link(int x, int p) { makeRoot(x), Splay(x), f[x] = p; }
void Split(int x, int p) { makeRoot(x), Access(p), Splay(p); }
void Cut(int x, int p)
{
    Split(x, p);
    if (ls == x && !ch[x][1])
        ls = f[x] = 0;
}
int Find(int p)
{
    Access(p), Splay(p);
    while (ls)
        pushDown(p), p = ls;
    Splay(p);
    return p;
}

int main()
{
#ifndef ONLINE_JUDGE
    freopen("x.in", "r", stdin);
#endif // !ONLINE_JUDGE
    n = read(), q = read();
    for (int i = 1; i <= n; i++)
        val[i] = read();
    for (int opt, x, y; q--;)
    {
        opt = read(), x = read(), y = read();
        if (opt == 0)
            Split(x, y), printf("%d\n", sum[y]);
        if (opt == 1)
        {
            int dx = Find(x), dy = Find(y);
            if (dx != dy)
                Link(x, y);
        }
        if (opt == 2)
            Cut(x, y);
        if (opt == 3)
            Split(x, x), val[x] = sum[x] = y;
    }
    return 0;
}
```
{{% /fold %}}

### [Luogu P3203] [HNOI2010]弹飞绵羊

学习一个精简 LCT

只会往后弹，意味着没有环，显然是森林

对于没有弹飞的点对，拉条单向边

询问操作转化为统计该点到原树树根的路径长度

断边连边用 LCT 维护即可

这里可以让代码更精简

对于查询操作，Access，Splay，输出 size 即可

对于连边操作，因为保证是一棵树，所以直接改父亲，连个虚边即可

对于断边操作，Access，Splay，x 就是辅助树树根，根据 splay 树性质（中序遍历单调递增），父亲就是 x 的左儿子

{{% fold "参考代码" %}}
```cpp
#include <stdio.h>
#include <algorithm>

using std::swap;

int read(int x = 0, int f = 0, char ch = getchar())
{
    while ('0' > ch or ch > '9')
        f = ch == '-', ch = getchar();
    while ('0' <= ch and ch <= '9')
        x = x * 10 + (ch ^ 48), ch = getchar();
    return f ? -x : x;
}

const int N = 2e5 + 5;

int n, q;
int ch[N][2], f[N], siz[N];

#define ls ch[p][0]
#define rs ch[p][1]
#define get(x) (ch[f[x]][1] == x)
#define isRoot(x) (ch[f[x]][0] != x and ch[f[x]][1] != x)
void pushUp(int p)
{
    siz[p] = siz[ls] + siz[rs] + 1;
}
void Rotate(int x)
{
    int y = f[x], z = f[y], k = get(x);
    if (!isRoot(y))
        ch[z][ch[z][1] == y] = x;
    ch[y][k] = ch[x][!k], f[ch[y][k]] = y;
    ch[x][!k] = y, f[y] = x, f[x] = z;
    pushUp(y), pushUp(x);
}
void Splay(int x)
{
    for (int fa; fa = f[x], !isRoot(x); Rotate(x))
        if (!isRoot(fa))
            Rotate(get(x) == get(fa) ? fa : x);
    pushUp(x);
}
int Access(int x, int p = 0)
{
    for (p = 0; x; p = x, x = f[x])
        Splay(x), ch[x][1] = p, pushUp(x);
    return p;
}

int main()
{
#ifndef ONLINE_JUDGE
    freopen("x.in", "r", stdin);
#endif // !ONLINE_JUDGE
    n = read();
    for (int i = 1, x; i <= n; i++)
    {
        siz[i] = 1, x = read();
        if (i + x <= n)
            f[i] = i + x;
    }
    for (q = read(); q--;)
    {
        int opt = read(), x = read() + 1;
        Access(x), Splay(x);
        if (opt == 1)
            printf("%d\n", siz[x]);
        else
        {
            int y = read();
            ch[x][0] = f[ch[x][0]] = 0;
            if (x + y <= n)
                f[x] = x + y;
            pushUp(x);
        }
    }
    return 0;
}
```
{{% /fold %}}