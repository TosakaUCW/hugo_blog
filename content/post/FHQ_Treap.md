+++
title = 'FHQ_Treap'
date = 2020-08-13T03:06:23+08:00
categories = ['算法']
tags = ['平衡树']
+++

FHQ yyds !

## FHQ Treap

极为简洁，无需各种勾八左旋右旋

FHQ Treap 就像普通 Treap 一样，每个节点除了 val 以外还有个随机权值 rnd，val 符合 BST 的特征，rnd 符合堆的特征

我们把左旋右旋丢掉，通过两种操作 split 和 merge 大力维护平衡树

<!--more-->

## 定义

为了方便阅读，先扔出定义代码

root 是全局根节点， num_node 是节点数量

```cpp
int root, num_node;

struct Treap_Node
{
#define ls(p) t[p].ls
#define rs(p) t[p].rs
#define val(p) t[p].val
#define rnd(p) t[p].rnd
#define size(p) t[p].size
    int ls, rs, rnd, val, size;
} t[N];

void update_size(int p) { size(p) = size(ls(p)) + size(rs(p)) + 1; }
```


## split

大力拆拆拆

一般我们有两种方式拆分一棵树，按 val 或者 size 把一棵树 split 成两坨，一坨扔到左边，一坨扔到右边，左边那坨树的根节点为 x，右边那坨树的根节点为y

这里拿 按 val 拆分举例

p为我们当前访问的点，假如 p 的 val 小于等于 k，我们就把他扔进左树（小于等于val），然后进入 p 的右子树（ BST 中 val 按中序递增），递归处理

反之同理

```cpp
void split(int p, int k, int &x, int &y)
{
    if (!p)
    {
        x = y = 0;
        return;
    }
    if (val(p) <= k)
    {
        x = p;
        split(rs(x), k, rs(x), y);
        update_size(x);
    }
    else
    {
        y = p;
        split(ls(y), k, x, ls(y));
        update_size(y);
    }
}
```

## merge

大力 mer mer merge

注意要按照 rnd 合并，才能保证树的期望高度

我这份代码是将 rnd 按照小根堆的形式 merge 的

别忘了 x 是我们之前 split 出来的左边那坨小树的根节点，y则反之

如果 rnd(x) 小于 rnd(y) 那么我们这个小根堆 x 就在上面对吧，把 y 和 x 的右子树扔到一起，递归 merge

反之同理


```cpp
int merge(int x, int y)
{
    if (!x or !y)
        return x | y;
    if (rnd(x) < rnd(y))
    {
        rs(x) = merge(rs(x), y);
        update_size(x);
        return x;
    }
    else
    {
        ls(y) = merge(x, ls(y));
        update_size(y);
        return y;
    }
}
```

## 插入一个值为 val 的节点

大力插插插

先 new_node 一个节点

然后我们大力 split 把树拆成 一坨比 val 小的 和 一坨比 val 大的

拆完了把这两棵树和我们刚刚新建的节点，总共三坨东西大力 merge


```cpp
void new_node(int &k, int val)
{
    val(++num_node) = val;
    size(num_node) = 1;
    rnd(num_node) = rand();
    ls(num_node) = rs(num_node) = 0;
    k = num_node;
}

void push(int &root, int val)
{
    int x = 0, y = 0, z = 0;
    split(root, val, x, y);
    new_node(z, val);
    root = merge(merge(x, z), y);
}
```

## 删除一个值为 val 的节点

大力删删删

和插入差不多

把树大力拆成 比 val 大的 和 比 val 小的

瞒着 val 偷偷 merge


```cpp
void pop(int &root, int val)
{
    int x = 0, y = 0, z = 0;
    split(root, val, x, z);
    split(x, val - 1, x, y);
    y = merge(ls(y), rs(y));
    root = merge(merge(x, y), z);
}
```

## 查询 val 的 rank

把小于 val 的节点大力 split 出来

看看 split 出来那坨东西的 size 大小就知道 rank 了

```cpp
int query_rank_by_val(int &root, int val)
{
    int x = 0, y = 0;
    split(root, val - 1, x, y);
    int rank = size(x) + 1;
    root = merge(x, y);
    return rank;
}
```

## 查询 rank 的 val

从根节点一路往下跑，康康什么时候当前节点的左子树的 size + 1 等于 rank 了，当前节点的 val 就是我们要找的，没跑到就递归下去跑

```cpp
int query_val_by_rank(int p, int rank)
{
    if (rank == size(ls(p)) + 1)
        return val(p);
    else if (rank <= size(ls(p)))
        return query_val_by_rank(ls(p), rank);
    else
        return query_val_by_rank(rs(p), rank - size(ls(p)) - 1);
}
```

## 查前驱

大力查查 val 的 rank，再查查 rank - 1 的 val 就好啦

```cpp
int query_prev(int &root, int val)
{
    int x = 0, y = 0, k = 0, prev = 0;
    split(root, val - 1, x, y);
    if (!x)
        return -INF;
    k = size(x);
    prev = query_val_by_rank(x, k);
    root = merge(x, y);
    return prev;
}
```

## 查后继

与前驱同理

```cpp
int query_next(int &root, int val)
{
    int x = 0, y = 0    , next = 0;
    split(root, val, x, y);
    if (!y)
        return INF;
    next = query_val_by_rank(y, 1);
    root = merge(x, y);
    return next;
}
```

## 平衡树模板题

```cpp
#include <stdio.h>
#include <algorithm>
#include <memory.h>
#include <stdlib.h>

const int N = 1e5 + 5;
const int INF = 2147483647;

int root, num_node;

struct Treap_Node
{
#define ls(p) t[p].ls
#define rs(p) t[p].rs
#define val(p) t[p].val
#define rnd(p) t[p].rnd
#define size(p) t[p].size
    int ls, rs, rnd, val, size;
} t[N];

void update_size(int p) { size(p) = size(ls(p)) + size(rs(p)) + 1; }

void new_node(int &k, int val)
{
    val(++num_node) = val;
    size(num_node) = 1;
    rnd(num_node) = rand();
    ls(num_node) = rs(num_node) = 0;
    k = num_node;
}

int merge(int x, int y)
{
    if (!x or !y)
        return x | y;
    if (rnd(x) < rnd(y))
    {
        rs(x) = merge(rs(x), y);
        update_size(x);
        return x;
    }
    else
    {
        ls(y) = merge(x, ls(y));
        update_size(y);
        return y;
    }
}

void split(int p, int k, int &x, int &y)
{
    if (!p)
    {
        x = y = 0;
        return;
    }
    if (val(p) <= k)
    {
        x = p;
        split(rs(x), k, rs(x), y);
        update_size(x);
    }
    else
    {
        y = p;
        split(ls(y), k, x, ls(y));
        update_size(y);
    }
}

void pop(int &root, int val)
{
    int x = 0, y = 0, z = 0;
    split(root, val, x, z);
    split(x, val - 1, x, y);
    y = merge(ls(y), rs(y));
    root = merge(merge(x, y), z);
}

void push(int &root, int val)
{
    int x = 0, y = 0, z = 0;
    split(root, val, x, y);
    new_node(z, val);
    root = merge(merge(x, z), y);
}

int query_val_by_rank(int p, int rank)
{
    if (rank == size(ls(p)) + 1)
        return val(p);
    else if (rank <= size(ls(p)))
        return query_val_by_rank(ls(p), rank);
    else
        return query_val_by_rank(rs(p), rank - size(ls(p)) - 1);
}

int query_rank_by_val(int &root, int val)
{
    int x = 0, y = 0;
    split(root, val - 1, x, y);
    int rank = size(x) + 1;
    root = merge(x, y);
    return rank;
}

int query_prev(int &root, int val)
{
    int x = 0, y = 0, k = 0, prev = 0;
    split(root, val - 1, x, y);
    if (!x)
        return -INF;
    k = size(x);
    prev = query_val_by_rank(x, k);
    root = merge(x, y);
    return prev;
}

int query_next(int &root, int val)
{
    int x = 0, y = 0    , next = 0;
    split(root, val, x, y);
    if (!y)
        return INF;
    next = query_val_by_rank(y, 1);
    root = merge(x, y);
    return next;
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
    int n = read();
    while (n--)
    {
        int opt = read(), x = read();
        switch (opt)
        {
        case 1:
            push(root, x);
            break;
        case 2:
            pop(root, x);
            break;
        case 3:
            printf("%d\n", query_rank_by_val(root, x));
            break;
        case 4:
            printf("%d\n", query_val_by_rank(root, x));
            break;
        case 5:
            printf("%d\n", query_prev(root, x));
            break;
        case 6:
            printf("%d\n", query_next(root, x));
            break;
        }
    }
    return 0;
}
```