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

时间复杂度 $O()$
