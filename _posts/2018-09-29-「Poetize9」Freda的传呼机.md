---
title: 「Poetize9」Freda的传呼机
date: 2018-09-29 12:00:00
categories:
- OI
tags:
- 最短路
- 倍增
mathjax: true
---

> 题目大意：给定一棵 $n$ 个节点 $m$ 条边的仙人掌，$q$ 次询问任意两点间的最短距离。$n,q \leq 10000,m\leq12000$

仙人掌最短路。可以建最短路树，也可以将环上的点与环根连边，当然也可以圆方树。

建出最短路树，则需要分类讨论的只有 $lca(x,y)$ 所在的环。如果 $lca(x,y)$ 下面的两个点在同一环内，那么 $lca(x,y)$ 也必定在这个环内，此时再倍增求 $x,y$ 所在的两棵子树与该环的交点，然后比较连接这两点的上半环与下半环的长度即可。

环染色时不染环根，可以处理一点多环的情况。

```c++
#include <cstdio>
#include <queue>
#include <cstring>
 
const int N = 10005, M = 24005;
 
struct Edge {
    int to, cost;
} e[M], g[M];
 
struct Pair {
    int x, y;
    bool operator < (const Pair &rhs) const {
        return x > rhs.x;
    }
};
 
int f[N][15], sum[N], dep[N], dis[N], n;
int head1[N], nxt1[M], tot1, head2[N], nxt2[M], tot2;
int sta1[N], sta2[N], top, dfn[N], col[N], len[N], dfs_clock, cnt;
bool vis[N];
 
std::priority_queue<Pair> Q;
 
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
void swap(int &x, int &y) {
    x ^= y, y ^= x, x ^= y;
}
void add_edge1(int u, int v, int w) {
    nxt1[++tot1] = head1[u];
    head1[u] = tot1;
    e[tot1] = (Edge){v, w};
}
void add_edge2(int u, int v, int w) {
    nxt2[++tot2] = head2[u];
    head2[u] = tot2;
    g[tot2] = (Edge){v, w};
}
void dijkstra() {
    bool vis[N] = {}; int pre[N], cost[N], cnt = 0;
    memset(dis, 0x3f, sizeof dis);
    dis[1] = 0, Q.push((Pair){0, 1});
    while (!Q.empty()) {
        int u = Q.top().y; Q.pop();
        if (vis[u]) continue;
        if (++cnt == n) break;
        vis[u] = 1;
        for (int i = head1[u]; i; i = nxt1[i])
            if (dis[e[i].to] > dis[u] + e[i].cost) {
                dis[e[i].to] = dis[u] + e[i].cost;
                pre[e[i].to] = u, cost[e[i].to] = e[i].cost;
                Q.push((Pair){dis[e[i].to], e[i].to});
            }
    }
    for (int i = 2; i <= n; ++i) add_edge2(pre[i], i, cost[i]);
}
void dfs(int cur, int father) { //找环
    sta1[++top] = cur, dfn[cur] = ++dfs_clock, vis[cur] = 1;
    for (int i = head1[cur]; i; i = nxt1[i]) {
        if (e[i].to == father) continue;
        if (!vis[e[i].to]) {
            sta2[top] = e[i].cost;
            dfs(e[i].to, cur);
        } else if (dfn[e[i].to] < dfn[cur]) {
            int j; ++cnt, sta2[top] = e[i].cost;
            for (j = top; sta1[j] != e[i].to; --j)
                col[sta1[j]] = cnt, len[cnt] += sta2[j];
            len[cnt] += sta2[j];
        }
    }
    --top;
}
void build(int cur, int fa, int deep) {
    f[cur][0] = fa, dep[cur] = deep;
    for (int i = 1; 1 << i <= deep; ++i)
        f[cur][i] = f[f[cur][i-1]][i-1];
    for (int i = head2[cur]; i; i = nxt2[i]) {
        sum[g[i].to] = sum[cur] + g[i].cost;
        build(g[i].to, cur, deep + 1);
    }
}
int query(int x, int y) {
    int xx = x, yy = y;
    if (dep[x] < dep[y]) swap(x, y), swap(xx, yy);
    for (int i = 14; i >= 0; --i)
        if (dep[f[x][i]] >= dep[y]) x = f[x][i];
    if (x == y) return sum[xx] - sum[x];
    for (int i = 14; i >= 0; --i)
        if (f[x][i] != f[y][i]) x = f[x][i], y = f[y][i];
    int dis = sum[xx] + sum[yy] - (sum[f[x][0]] << 1);
    if (!col[x] || col[x] != col[y]) return dis;
    int a = x, b = y; x = xx, y = yy;
    for (int i = 14; i >= 0; --i)
        if (dep[f[x][i]] > dep[a] && col[f[x][i]] != col[a]) x = f[x][i];
    for (int i = 14; i >= 0; --i)
        if (dep[f[y][i]] > dep[b] && col[f[y][i]] != col[b]) y = f[y][i];
    if (col[x] != col[a]) x = f[x][0];
    if (col[y] != col[b]) y = f[y][0];
    return min(dis, len[col[a]] - dis + (sum[xx] - sum[x] + sum[yy] - sum[y] << 1));
}
 
int main() {
    n = read(); int m = read(), q = read();
    for (int i = 1; i <= m; ++i) {
        int u = read(), v = read(), w = read();
        add_edge1(u, v, w), add_edge1(v, u, w);
    }
    dijkstra(), dfs(1, 0), build(1, 0, 0);
    while (q--) {
        int x = read(), y = read();
        printf("%d\n", query(x, y));
    }
    return 0;
}
```

时间复杂度 $O((n+m+q)\log n)$
