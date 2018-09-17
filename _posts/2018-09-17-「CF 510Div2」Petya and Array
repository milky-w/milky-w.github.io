---
title: 「CF 510Div2」Petya and Array
date: 2018-09-17 18:33:00
categories:
- OI
tags:
- 平衡树
mathjax: true
---

> 题目大意：给定一个长度为 $n$ 的序列 $\{a\}$，问有多少个和小于 $t$ 的区间。$n \leq 200000, \|a_i\| \leq 10^9$

前缀的后缀是子串，即前缀减前缀是子串。

从前往后依次将前缀 $0$、前缀 $1$、前缀 $2$ …… 前缀 $n$ 的和加入平衡树中，加入前缀 $i$ 时，有 $sum[i]-x<t \Rightarrow x>sum[i]-t$，因此在平衡树中查询前 $i-1$ 个前缀中有多少个的和是大于 $sum[i]-t$ 的即可。

```c++
#include <cstdio>
#include <cstdlib>

typedef long long LL;
const LL inf = 1e18;
const int N = 200005;
LL val[N], t, sum, ans;
int siz[N], num[N], rnd[N], ch[N][2], root, cnt, n;

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
LL min(LL x, LL y) {
	return x < y ? x : y;
}
void newNode(int &cur, LL x) {
	cur = ++cnt;
	siz[cur] = num[cur] = 1;
	val[cur] = x, rnd[cur] = rand();
}
int cmp(int cur, LL x) {
	if (val[cur] == x) return -1;
	return x < val[cur] ? 0 : 1;
}
void maintain(int &cur) {
	siz[cur] = num[cur] + siz[ch[cur][0]] + siz[ch[cur][1]];
}
void rotate(int &cur, int k) {
	int fa = ch[cur][k^1];
	ch[cur][k^1] = ch[fa][k], ch[fa][k] = cur;
	maintain(cur), maintain(fa), cur = fa;
}
void insert(int &cur, LL x) {
	if (!cur) newNode(cur, x);
	else {
		int k = cmp(cur, x);
		if (k == -1) ++num[cur];
		else {
			insert(ch[cur][k], x);
			if (rnd[ch[cur][k]] > rnd[cur]) rotate(cur, k ^ 1);
		}
		maintain(cur);
	}
}
int suf(int cur, LL x) {
	if (!cur) return 0;
	if (val[cur] <= x) return suf(ch[cur][1], x);
	return num[cur] + siz[ch[cur][1]] + suf(ch[cur][0], x);
}

int main() {
	scanf("%d%lld", &n, &t);
	insert(root, 0);
	for (int i = 1; i <= n; ++i) {
		sum += read();
		ans += suf(root, sum - t);
		insert(root, sum);
	}
	printf("%lld", ans);
	return 0;
}
```

时间复杂度 $O(n \log n)$
