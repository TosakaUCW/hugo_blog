+++
title = "Luogu P2261 余数求和（除法分块）"
categories = ["题解"]
tags = ["数论", "除法分块"]
date = "2018-12-08T23:25:30+08:00"
description = ""
aliases = ["/post/Luogu_P2261", "Luogu_P2261"]
+++

[题目链接](https://www.luogu.org/problemnew/show/P2261)

{{% question %}}
给定正整数\`n\`和\`k\`

求 \`ans = \sum_{i = 1}^n k\%i\`

要求 \`\mathcal{O}(\sqrt{k})\` 时间复杂度
{{% /question %}}

<!--more-->

## 简要做法

$$ans = \sum_{i = 1}^n k\%i$$

$$ans = \sum_{i = 1}^n(k- i \times {\lfloor\frac{k}{i}\rfloor})$$

$$ans = n \times k - \sum_{i = 1}^n (i \times \lfloor\frac{k}{i}\rfloor)$$

但似乎到这里我们就不会做了

但是经验告诉我们

数论先打表

于是将\`\lfloor\frac{k}{i}\rfloor\`打表

当\`n = 10 , k = 5\`时

|             \`i\`             | \`1\` | \`2\` | \`3\` | \`4\` | \`5\` | \`6\` | \`7\` | \`8\` | \`9\` | \`10\` |
| :---------------------------: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :----: |
| \`\lfloor\frac{k}{i}\rfloor\` | \`5\` | \`2\` | \`1\` | \`1\` | \`1\` | \`0\` | \`0\` | \`0\` | \`0\` | \`0\`  |

此时我们发现\`\lfloor\frac{k}{i}\rfloor\`被分成了连续的几块

于是便可以用 \`n \times k\` 减去每一块的和求出答案

设\`l\`代表当前块的左端点

设\`r\`代表当前块的右端点

此时枚举\`l\`

即可通过 \`l\` 和 \`k\` 求出 \`r\`

设\`t = \lfloor\frac{k}{i}\rfloor\`

- 若\`t  \ne 0\`

	则 \`r = min(\lfloor\frac{k}{t}\rfloor,n)\`
- 若\`t = 0\`

	则 \`r = n\`

每一块的和 \`=\` 当前块的 \`t\` \`\times\` 当前块元素个数 \`\times\` 当前块i的平均值\`= t \times (r - l + 1) \times (l+r) \div 2\`

处理完当前块后令 \` l = r + 1 \` 继续处理下一块即可

复杂度分析

对于 \`\lfloor\frac{k}{i}\rfloor\`

- 若 \`i \le \sqrt k\`
	最多只有 \` \sqrt k \` 种取值
- 若 \`i > \sqrt k\`
	则 \`\lfloor\frac{k}{i}\rfloor < \sqrt k\`
    取值也不超过 \`\sqrt k\` 种

故 \`\lfloor\frac{k}{i}\rfloor\` 最多只有 \`2 \times \sqrt k\` 种取值

所以整除分块的时间复杂度为\`\mathcal{O}(\sqrt{k})\`

## 参考代码

```c++
#include <stdio.h>
#include <algorithm>

long long n, k, ans;

int main()
{
    scanf("%lld%lld", &n, &k);
    ans = n * k;
    for (long long l = 1, r; l <= n; l = r + 1)
    {
        long long t = k / l;
        if (t == 0)
            r = n;
        else
            r = std::min(k / t, n);
        ans -= t * (r - l + 1) * (l + r) / 2;
    }
    printf("%lld", ans);
    return 0;
}
```