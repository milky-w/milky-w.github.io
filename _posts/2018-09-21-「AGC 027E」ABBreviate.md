---
title: 「AGC 027E」ABBreviate
date: 2018-09-21 14:23:00
categories:
- OI
tags:
- 动态规划
mathjax: true
---

> 题目大意：给定一个仅由 $a,b$ 构成的字符串 $s$，每次操作可以把 $aa$ 换成 $b$，也可以把 $bb$ 换成 $a$，问任意次操作可以得到几种不同的字符串。$\|s\| \leq 10^5$

如果用 $1$ 来表示 $a$，$2$ 来表示 $b$，那么任意次操作后，整个串的和在模 $3$ 的意义下的值是不变的，我们称这个值为 $p$ 值。

则问题可以转化成，找有多少个字符串 $t$，使得将 $s$ 分成 $\|t\|$ 段后，第 $i$ 段的 $p$ 值与 $t[i]$ 的 $p$ 值相等。我们可以贪心的构造这样的 $t$：让每一段的长度尽可能的小，如果分出 $\|t\|$ 段后，剩余部分的 $p$ 值为 $0$，那就是一个合法的 $t$。（$p$ 值为 $0$ 的段可以与任意段结合）

从后往前转移，设 $f[i]$ 表示前 $i$ 个字符看作一段的方案数，如果 $p(s_{i+1} \cdots s_n)=0$，那么 $i$ 之后的位置可以看成是剩余的位置，$f[i]$ 的初值为 $1$，否则为 $0$；$f[i]$ 可以从后面最近的 $p$ 值为 $p(s_1 \cdots s_i) + 1$ 和 $p(s_1 \cdots s_i) + 2$ 的位置转移。

```c++
#include <cstdio>
#include <cstring>

const int N = 100005, P = 1e9+7;
char s[N]; int sum[N], f[N], pre[3], n, flag;

int main() {
	scanf("%s", &s); n = strlen(s);
	for (int i = 1; i < n; ++i) //判断是否可以操作
		if (s[i] == s[i-1]) { flag = 1; break; }
	if (!flag) { puts("1"); return 0; }
	for (int i = 0; i < n; ++i) sum[i+1] = s[i] - 'a' + 1;
	for (int i = 2; i <= n; ++i) sum[i] = (sum[i] + sum[i-1]) % 3;
	f[n] = 1;
	for (int i = 0; i <= 2; ++i) pre[i] = (i == sum[n] ? n : n + 1);
	for (int i = n - 1; i; --i) {
		f[i] = (sum[i] == sum[n]); //后面的p值是否为0
		for (int j = 1; j <= 2; ++j)
			f[i] = (f[i] + f[pre[(sum[i]+j)%3]]) % P; //从后面转移
		pre[sum[i]] = i; //记录该p值上一次出现的位置
	}
	printf("%d\n", (f[pre[1]] + f[pre[2]]) % P);
	return 0;
}
```

时间复杂度 $O(n)$
