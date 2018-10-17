---
title: 「ARC 075E」Meaningful Mean
date: 2018-10-11 21:00:00
categories:
- OI
tags:
- 树状数组
- 逆序对
- 前缀和
mathjax: true
---

> 题目大意：给定一个长度为 $n$ 的序列 $a$，问 $a$ 中有多少个区间的平均值不小于 $k$。$n \leq 2 \times 10^5$

将每个数都减 $k$，转化为求和大于等于 $0$ 的区间个数，考虑在前缀和数组中的两个位置 $l,r$，如果 $sum[r] \geq sum[l-1]$，说明区间 $[l,r]$ 的和是大于等于 $0$ 的，树状数组求顺序对个数即可。

```c++
#include <cstdio>
#include <algorithm>

typedef long long LL;
const int N = 200005;
int n, k; LL a[N], b[N], sum[N], ans;

int read() {
    int x = 0; char c = getchar();
    while (c < '0' || c > '9') c = getchar();
    while (c >= '0' && c <= '9') {
        x = (x << 3) + (x << 1) + (c ^ 48);
        c = getchar();
    }
    return x;
}
int find(LL x) {
    int l = 1, r = n;
    while (l <= r) {
        int mid = l + (r - l >> 1);
        if (a[mid] == x) return mid;
        if (a[mid] < x) l = mid + 1;
        else r = mid - 1;
    }
}
int lowbit(int x) {
    return x & (-x);
}
int query(int x) {
    int res = 0;
    while (x) res += b[x], x -= lowbit(x);
    return res;
}
void update(int x) {
    while (x <= n) ++b[x], x += lowbit(x);
}

int main() {
    n = read(), k = read();
    for (int i = 1; i <= n; ++i) {
        a[i] = sum[i] = sum[i-1] + read() - k;
        if (sum[i] >= 0) ++ans;
    }
    std::sort(a + 1, a + n + 1);
    for (int i = 1; i <= n; ++i) {
        int x = find(sum[i]);
        ans += query(x); update(x);
    }
    printf("%lld\n", ans);
    return 0;
}
```

时间复杂度 $O(n \log n)$
