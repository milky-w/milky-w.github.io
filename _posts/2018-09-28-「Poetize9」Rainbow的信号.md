---
title: 「Poetize9」Rainbow的信号
date: 2018-09-28 12:00:00
categories:
- OI
tags:
- 前缀和
mathjax: true
---

> 题目大意：给定一个长度为 $n$ 的序列，每个数都是非负整数，等概率地选择 $l$ 和 $r$，如果 $l > r$ 则交换 $l, r$，请你输出区间 $[l, r]$ 的期望 $xor$ 和，$and$ 和，$or$ 和。$n \leq 10^5$

按照二进制的每一位进行处理，其中 $xor$ 类似于「CF 510Div2D」的做法，$and$ 和 $or$ 分别是查询序列中连续的 $1$ 和 $0$。

```c++
#include <cstdio>
 
typedef long long LL;
LL ans1, ans2, ans3;
int n, mx, a[100005];
 
int read() {
    int x = 0; char c = getchar();
    while (c < '0' || c > '9') c = getchar();
    while (c >= '0' && c <= '9') {
        x = (x << 3) + (x << 1) + (c ^ 48);
        c = getchar();
    }
    return x;
}
int max(int x, int y) {
    return x > y ? x : y;
}
void calc_xor(int k) {
    int zero = 0, one = 0, sum = 0; LL cnt = 0;
    for (int i = 1; i <= n; ++i) {
        sum ^= a[i] & k;
        if (sum) cnt += (zero + 1) << 1, ++one;
        else cnt += one << 1, ++zero;
        if (a[i] & k) --cnt;
    }
    ans1 += cnt * k;
}
void calc_and(int k) {
    LL cnt = 0; int j = 0;
    for (int i = 1; i <= n; ++i) {
        if (a[i] & k) {
            if (j == 0) j = i;
        } else if (j) {
            cnt += 1LL * (i - j) * (i - j);
            j = 0;
        }
    }
    if (j) cnt += 1LL * (n + 1 - j) * (n + 1 - j);
    ans2 += cnt * k;
}
void calc_or(int k) {
    LL cnt = 0; int j = 0;
    for (int i = 1; i <= n; ++i) {
        if (a[i] & k) {
            if (j) cnt += 1LL * (i - j) * (i - j);
            j = 0;
        } else {
            if (j == 0) j = i;
        }
    }
    if (j) cnt += 1LL * (n + 1 - j) * (n + 1 - j);
    ans3 += (1LL * n * n - cnt) * k;
}
 
int main() {
    n = read(), mx = 0;
    for (int i = 1; i <= n; ++i)
        a[i] = read(), mx = max(mx, a[i]);
    for (int i = 0; i <= 30; ++i) {
        if (1 << i > mx) break;
        calc_xor(1 << i), calc_and(1 << i), calc_or(1 << i);
    }
    printf("%.3lf %.3lf %.3lf\n", ans1 * 1.0 / n / n, ans2 * 1.0 / n / n, ans3 * 1.0 / n / n);
    return 0;
}
```

时间复杂度 $O(n \log 10^9)$
