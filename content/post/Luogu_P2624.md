+++
title = "Luogu P2624 [HNOI2008]明明的烦恼（Prufer 序列）"
categories = ["题解"]
tags = ["Prufer 序列"]
date = "2019-01-30T23:25:30+08:00"
description = ""
aliases = ["/post/Luogu_P2624", "/Luogu_P2624"]
+++

[题目链接](https://www.luogu.org/problemnew/show/P2624)

{{% question %}}
给出标号为 1 到 N 的点，以及某些点最终的度数

允许在任意两点间连线，可产生多少棵度数满足要求的树？

\`n \le 1000\`
{{% /question %}}

<!--more-->

## 简要做法

**Prufer Sequence**

一棵顶点标号（1~n）树，可以用一个叫做 Purfer Sequence 的数列表示

一个含有 n 个节点的 Purfer Sequence 有 n-2 个数

Purfer Sequence 中的每个数是 1~n 中的一个数

一个 Purfer Sequence 和一棵树一一对应

### Tree to Prufer Sequence

总共需要 n-2 步

每一步都在当前的树中寻找具有最小标号的叶子节点（度为 1）

将与其相连的点的标号设为 Purfer Sequence 的第 i 个元素，并将此叶子节点从树中删除

直到最后得到一个长度为 n-2 的 Purfer Sequence 和一个只有两个节点的树

文字描述的过程有点复杂，不妨自行构造一棵树手模一下就会发现很简单

### Prufer Sequence to Tree

先将所有编号为 1 到 n 的点的度赋初值为 1，然后加上它在 Purfer Sequence 中出现的次数，得到每个点的度

先执行 n-2 步，每一步，选取具有最小标号的度为 1 的点 u 与 Purfer Sequence 中的第 i 个数 v 表示的顶点相连，得到树中的一条边，并将 u 和 v 的度减一

最后再把剩下的两个度为 1 的点连边，加入到树中

文字描述的过程有点复杂，不妨自行构造一棵树手模一下就会发现很简单

### [HNOI2008]明明的烦恼

对于\`cnt\`个度数限制为\`d_i\`的点，那么它会在prufer序列中出现\`d_i-1\`次

故Prufer Sequence的总长度\`sum=\sum_{i=1}^{cnt}(d_i-1)\`

所以Prufer Sequence不同的排列总数为

$$ C_{n-2}^{sum} \times \frac{sum!}{\prod_{i=1}^{cnt}(d_i-1)!} $$

而剩下(n-2-sum)个位置可以随意排列(n-cnt)个点

所以

$$ ans = C_{n-2}^{sum} \times \frac{sum!}{\prod_{i=1}^{cnt}(d_i-1)!} \times (n-cnt)^{(n-2-sum)} $$

化简得

$$ ans = \frac{(n-2)!}{(n-2-sum)! \times \prod_{i=1}^{cnt}(d_i-1)!} \times (n-cnt)^{(n-2-sum)} $$

由于\`n \le 1000\`，需要使用高精

## 参考代码

```python
from math import factorial


def C(n, m):
    if m > n:
        return 0
    return factorial(n) // factorial(m) // factorial(n - m)


def pow(x, k):
    res = 1
    while k > 0:
        if k & 1:
            res *= x
        x *= x
        k >>= 1
    return res


n = int(input())
k, Sum = 0, 0
a = []

for i in range(n):
    x = int(input())
    if x == -1:
        k += 1
    else:
        x -= 1
        Sum += x
        a.append(x)

ans = C(n - 2, Sum) * factorial(Sum)
for x in a:
    ans = ans // factorial(x)
ans *= pow(k, n - 2 - Sum)

print(ans)
```

## 参考文献
- http://www.matrix67.com/blog/archives/682
- http://www.cnblogs.com/zhj5chengfeng/archive/2013/08/23/3278557.html