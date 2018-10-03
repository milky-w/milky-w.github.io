---
title: 「HDU 6315」Naive Operations
date: 2018-10-03 11:00:00
categories:
- OI
tags:
- 线段树
mathjax: true
---

> 题目大意：给定一个长度为 $n$ 排列 $b$，$q$ 次操作，每次将序列 $a$ 的区间 $[l,r]$ 加 $1$，或询问 $\sum\limits_{i=l}^{r}\lfloor\frac{a_i}{b_i}\rfloor$ 的值。（$a$ 初始时全是 $0$）$1 \leq n,q \leq 10^5$

$a_i$ 从 $0$ 加到 $b_i$ 相当于 $b_i$ 减到 $0$，因此可以线段树区间减，并维护区间最小值，如果最小值为 $0$，则递归到叶子节点累计答案并重新复制为 $b_i$。

考虑 $b$ 是一个排列，那么答案最大为 $\frac{n}{1}+\frac{n}{2}+\cdots+\frac{n}{n} \approx n \log n$，而每累计一次答案的时间复杂度为 $\log n$，因此总复杂度为 $O(n\log^2n)$。

```c++
#include <cstdio>
#include <cstring>

const int N = 100005;
int b[N], mn[N<<2], ans[N<<2], tag[N<<2];

int read() {
	int x = 0; char c = getchar();
	while (c < '0' || c > '9') c = getchar();
	while (c >= '0' && c <= '9') {
		x = (x << 3) + (x << 1) + (c ^ 48);
		c = getchar();
	}
	return x;
}
int min(int x, int y) {
	return x < y ? x : y;
}
void maintain(int cur) {
	mn[cur] = min(mn[cur<<1], mn[cur<<1|1]);
	ans[cur] = ans[cur<<1] + ans[cur<<1|1];
}
void pushdown(int cur, int l, int r) {
	if (l < r) {
		mn[cur<<1] -= tag[cur], mn[cur<<1|1] -= tag[cur];
		tag[cur<<1] += tag[cur], tag[cur<<1|1] += tag[cur];
	}
	tag[cur] = 0;
}
void build(int cur, int l, int r) {
	if (l == r) mn[cur] = b[l];
	else {
		int mid = l + (r - l >> 1);
		build(cur << 1, l, mid);
		build(cur << 1 | 1, mid + 1, r);
		maintain(cur);
	}
}
void modify(int cur, int l, int r) {
	if (l == r) ++ans[cur], mn[cur] = b[l];
	else {
		pushdown(cur, l, r);
		int mid = l + (r - l >> 1);
		if (mn[cur<<1] == 0) modify(cur<<1, l, mid);
		if (mn[cur<<1|1] == 0) modify(cur<<1|1, mid + 1, r);
		maintain(cur);
	}
}
void update(int cur, int l, int r, int L, int R) {
	if (L <= l && r <= R) {
		--mn[cur], ++tag[cur];
		if (mn[cur] == 0) modify(cur, l, r);
	} else {
		pushdown(cur, l, r);
		int mid = l + (r - l >> 1);
		if (L <= mid) update(cur << 1, l, mid, L, R);
		if (mid < R) update(cur << 1 | 1, mid + 1, r, L, R);
		maintain(cur);
	}
}
int query(int cur, int l, int r, int L, int R) {
	if (L <= l && r <= R) return ans[cur];
	pushdown(cur, l, r);
	int mid = l + (r - l >> 1), res = 0;
	if (L <= mid) res += query(cur << 1, l, mid, L, R);
	if (mid < R) res += query(cur << 1 | 1, mid + 1, r, L, R);
	return res;
}

int main() {
	int n, m;
	while (~scanf("%d%d", &n, &m)) {
		for (int i = 1; i <= n; ++i) b[i] = read();
		memset(mn, 0x3f, sizeof mn);
		memset(tag, 0, sizeof tag);
		memset(ans, 0, sizeof ans);
		build(1, 1, n);
		while (m--) {
			char s[10]; scanf("%s", s);
			int x = read(), y = read();
			if (s[0] == 'a') update(1, 1, n, x, y);
			else printf("%d\n", query(1, 1, n, x, y));
		}
	}
	return 0;
}
```

时间复杂度 $O(n\log^2n)$
