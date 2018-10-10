---
title: 「POJ Challenge」消失之物
date: 2018-10-09 22:00:00
categories:
- OI
tags:
- DP套DP
mathjax: true
---

> 题目大意：

先做一遍01背包，得到 $f$ 数组，其中 $f[i]$ 表示装满容量为 $i$ 的背包的方案数。

然后枚举当前物品，减掉它的贡献，$g[j] -= g[j-w[i]]$ 相当于减去用 $w[i]$ 装满容量为 $j$ 的背包的方案数，而 $j$ 要顺序枚举的原因是，我们要保证 $g[j-w[i]]$ 的方案数里已经不存在 $w[i]$ 了。

```c++
#include <cstdio>
#include <cstring>

int f[2005], g[2005], w[2005], n, m;

int main() {
    scanf("%d %d", &n, &m);
    for (int i = 1; i <= n; ++i) scanf("%d", &w[i]);
    f[0] = 1;
    for (int i = 1; i <= n; ++i)
        for (int j = m; j >= w[i]; --j) (f[j] += f[j-w[i]]) %= 10;
    for (int i = 1; i <= n; ++i) {
        memcpy(g, f, sizeof f);
        for (int j = w[i]; j <= m; ++j) g[j] = (g[j] - g[j-w[i]] + 10) % 10;
        for (int j = 1; j <= m; ++j) printf("%d", g[j]);
        puts("");
    }
    return 0;
}
```

时间复杂度 $O(nm)$
