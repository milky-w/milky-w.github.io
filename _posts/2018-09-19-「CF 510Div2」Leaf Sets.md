---
title: 「CF 510Div2」Leaf Sets
date: 2018-09-19 14:43:00
categories:
- OI
tags:
- 贪心
mathjax: true
---

> 题目大意：给定一棵 $n$ 个节点的树，每条边的长度为 $1$，定义叶子节点为只与一个节点相邻的节点，请你将全部叶子节点划分到 $m$ 个互不相交的集合，使得在同一集合内的节点两两之间的距离不超过 $k$，问 $m$ 最小是多少？$3 \leq n \leq 10^6, 1 \leq k \leq 10^6$

dfs，从底部往上依次合并叶子节点。

令 $dep[i]$ 表示子树 $i$ 中最深的可合并的叶子节点的深度，设 $i$ 有两个儿子 $a,b$，若 $dep[a]+dep[b]\leq k$，则合并这两个集合，$dep[i]=\max(dep[a],dep[b])$；

否则，若 $dep[a]+dep[b]>k$，不妨设 $dep[a]<dep[b]$，那么后面能合并到 $b$ 的节点，一定不如合并到 $a$ 更优，因为

$$
dep[a]+dep[b]>k \Rightarrow dep[a]>k-dep[b]
$$

若存在

$$
dep[b]+dep[c] \leq k \Rightarrow dep[c] \leq k - dep[b]
$$

必有

$$
dep[c] < dep[a]
$$

此时令 $dep[i]=\min(dep[a],dep[b])$ 即可。

实现时需注意，如果 $u$ 不是叶子节点且 $dep[u]=0$，说明子树 $u$ 的全部叶子节点都不能再继续往上合并了，此时应将 $dep[u]$ 设为 $\inf$。

```c++
#include <cstdio>

const int N = 1e6+5;
int head[N], pre[N<<1], nxt[N<<1], dep[N], tot, k, cnt;

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
int min(int x, int y) {
	return x < y ? x : y;
}
void add_edge(int u, int v) {
	pre[++tot] = head[u];
	head[u] = tot, nxt[tot] = v;
}
void dfs(int cur, int fa) {
	for (int i = head[cur]; i; i = pre[i])
		if (nxt[i] != fa) {
			dfs(nxt[i], cur);
			if (dep[cur] + dep[nxt[i]] + 1 <= k) {
				if (dep[cur]) --cnt; //合并一个后减掉
				dep[cur] = max(dep[cur], dep[nxt[i]] + 1);
			} else {
				dep[cur] = min(dep[cur], dep[nxt[i]] + 1);
			}
		}
	if (!pre[head[cur]]) ++cnt; //记录有几个叶子节点
	else if (dep[cur] == 0) dep[cur] = k; //如果不是叶子节点且dep为0, 则不能继续往上统计
}

int main() {
	int n = read(); k = read();
	for (int i = 1; i < n; ++i) {
		int u = read(), v = read();
		add_edge(u, v), add_edge(v, u);
	}
	for (int i = 1; i <= n; ++i)
		if (pre[head[i]]) { //根不能为叶子节点
			dfs(i, 0); break;
		}
	printf("%d\n", cnt);
	return 0;
}
```

时间复杂度 $O(n)$
