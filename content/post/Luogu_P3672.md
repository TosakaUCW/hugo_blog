+++
title = "Luogu P3672 小清新签到题（DP）"
categories = ["题解"]
tags = ["DP"]
date = "2019-01-27T23:25:30+08:00"
description = ""
aliases = ["/post/Luogu_P3672", "/Luogu_P3672"]
+++

[题目链接](https://www.luogu.org/problemnew/show/P3672)

{{% question %}}
给定自然数\`n,k,x\`，你要求出第\`k\`小的长度为\`n\`的逆序对对数为\`x\`的1 ~ n的排列

\`n \le 300, k \le 10^{13}\`
{{% /question %}}

<!--more-->

## 简要做法

设\`f(i,j)\`表示第\`i\`位，逆序对数为\`j\`的方案数

那么\`f(i,j)=\sum_{k=0}^{i-1}f(i-1,j-k)\`

前缀和优化一下就是\`f(i,j)=sum(i-1,j)-sum(i-1,j-i)\`

因为\`f(i,j)\`会爆`long long`，但是\`k \le 10^{13}\`

所以当\`f(i,j) > k\`时，设成\`k+1\`就行了

还有个就是可能会爆空间

于是把sum滚起来就行了

然后DP完毕直接枚举即可

枚举每一位可以放什么数，统计放这个数所造成的贡献c，以及之前可以放的数的方案数tmp。

则当\`f[i-1][x-c]+tmp \ge k\`的时候就可以放这个数了。

## 参考代码

```c++
#include <stdio.h>

typedef long long LL;

const int N = 300 + 5;
const int X = N * (N - 1) / 2;

int n, x;
int ans[N], vis[N];
LL k;
LL f[N][X], sum[2][X];

int main()
{
    scanf("%d%lld%d", &n, &k, &x);
    int p = 0;
    f[0][0] = 1;
    for (int i = 0; i <= x; i++)
        sum[p][i] = 1;
    for (int i = 1; i <= n; i++, p ^= 1)
        for (int j = 0; j <= x; j++)
        {
            if (j - i + 1 > 0)
                f[i][j] = sum[p][j] - sum[p][j - i];
            else
                f[i][j] = sum[p][j];
            if (f[i][j] > k)
                f[i][j] = k + 1;
            sum[p ^ 1][j] = f[i][j] + (j ? sum[p ^ 1][j - 1] : 0);
        }
    for (int i = n; i >= 1; i--)
    {
        LL tmp = 0;
        for (int j = 1; j <= n; j++)
            if (!vis[j])
            {
                int c = j - 1;
                for (int l = 1; l < j; l++)
                    c -= vis[l];
                if (f[i - 1][x - c] + tmp >= k)
                {
                    ans[n - i + 1] = j;
                    vis[j] = 1;
                    x -= c;
                    k -= tmp;
                    break;
                }
                tmp += f[i - 1][x - c];
            }
    }
    for (int i = 1; i <= n; i++)
        printf("%d ", ans[i]);
    return 0;
}
```

## 参考文献
- https://www.cnblogs.com/candy99/p/6582699.html
- http://www.cnblogs.com/luyouqi233/p/9103900.html