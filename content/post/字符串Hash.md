+++
title = '字符串Hash'
date = 2020-08-13T03:28:13+08:00
categories = ['算法']
tags = ['Hash']
+++

摸你赛的时候傻逼了打了个暴力 std::unordered_map，没打快速子串 hash

<!--more-->

## BKDR Hash

### 子串 hash

$hash(l,r) = \sum_{i=l}^{r}{str_i} base^{r-i}$

$hash(l,r) = (\sum_{i=1}^{r}{str_i} base^{r-i}) - (\sum_{i=1}^{i-1}{str_i} base^{l-1-i}) \times base^{r-l+1}$

$hash(l,r) = hash(1,r) - hash(1,l-1) \times base^{r-l+1}$

$O(n)$ 维护 $base^n$ 次方和 hash 前缀和即可 $O(1)$ 查询

### 子串拼接

将 $[l1,r1]$ 和 $[l2,r2]$ 拼接在一起后的 $hash(l,r)$

$hash(l,r)=hash(l_1,r_1) \times base^{r_2-l_2+1}  + hash(l_2,r_2)$

注意根据 BKDR hash 的算法进行变形，比如 O(1) 求子串拼接 hash ，删除字符 hash 等

代码使用unsigned long long 自然溢出(相当于$\bmod 2^{64}$)，看情况使用多模数 hash

## 参考代码

```cpp
#include <stdio.h>

typedef unsigned long long ull;

const int N = 1e5 + 5;
const ull base = 19260817;

int n;
ull pow[N], sum[N];
char a[N];

// 查询子串 hash
ull hash(int L, int R) { return sum[R] - sum[L - 1] * pow[R - L + 1]; }

int main()
{
    n = read()；
    scanf("%s", a + 1);
    pow[0] = 1;
    for (int i = 1; i <= n; i++)
    {
        pow[i] = pow[i - 1] * base;
        sum[i] = sum[i - 1] * base + a[i];
    }
}
```
