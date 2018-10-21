---
title: 「POJ Challenge」生日礼物
date: 2018-10-22 07:00:00
categories:
- OI
tags:
- 堆
- 贪心
mathjax: true
---

> 题目大意：

把连续的符号相同的缩成一个数字，把两端的非正数去掉，得到一个正负交替的序列，把该序列中所有数的绝对值扔进堆里，把所有的正数加起来，再减掉一个最小值，这个最小值的求法与「[CTSC 2007 数据备份](https://milky-w.github.io/oi/2018/10/21/CTSC-2007-%E6%95%B0%E6%8D%AE%E5%A4%87%E4%BB%BD/#more)」的做法相同。

```c++
#include <cstdio>
#include <queue>
 
const int N = 100005, inf = 0x3f3f3f3f;
struct Node {
    int x, y;
    bool operator < (const Node &rhs) const {
        return x > rhs.x;
    }
};
int a[N], L[N], R[N], vis[N];
std::priority_queue<Node> Q;
 
int read() {
    int x = 0, f = 1;
    char c = getchar();
    while (c < '0' || c > '9') {
        if (c == '-') f = -1;
        c = getchar();
    }
    while (c >= '0' && c <= '9') {
        x = (x << 3) + (x << 1) + (c ^ 48);
        c = getchar();
    }
    return x * f;
}
 
int main() {
    int n = 0, m = read(), t = read(), ans = 0;
    for (int i = 1; i <= m; ++i) {
        int x = read();
        if (!n && x <= 0) continue;
        if (!n || x * a[n] < 0) a[++n] = x;
        else a[n] += x;
    }
    if (a[n] <= 0) --n;
    m = t;
    for (int i = 1; i <= n; ++i) {
        if (i & 1) ans += a[i];
        else a[i] = -a[i];
        Q.push((Node){a[i], i});
        L[i] = i - 1, R[i] = i + 1;
    }
    a[0] = a[n+1] = a[n+2] = inf, L[0] = R[n+1] = n + 2;
    n = (n + 1) / 2;
    for (int i = 1; i <= n - m; ++i) {
        Node now = Q.top();
        while (vis[now.y]) Q.pop(), now = Q.top();
        Q.pop(), ans -= now.x;
        int l = L[now.y], r = R[now.y];
        vis[l] = vis[r] = true;
        L[now.y] = L[l], R[now.y] = R[r], L[R[now.y]] = R[L[now.y]] = now.y;
        a[now.y] = a[l] + a[r] - a[now.y];
        Q.push((Node){a[now.y], now.y});
    }
    printf("%d\n", ans);
    return 0;
}
```
