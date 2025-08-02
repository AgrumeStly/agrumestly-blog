---
title: Luogu[P1967] [NOIP2013 提高组] 货车运输
date: 2023-05-13 22:17:48
tags:
    - 图论
    - 贪心
    - 倍增
    - 并查集
    - 生成树
    - LCA最近公共祖先
    - Luogu
    - NOIP
categories: 题目解析
---


[Link→](https://www.luogu.com.cn/problem/P1967)

<!-- more -->

很容易想到一个暴力做法，就是跑一遍 Floyd，$F_{i,j}$ 表示 $i$ 到 $j$ 最大载重量，转移 $F_{i,j}=\max\{F_{i, j}, \min\{F_{i, k}, F_{k,j}\}\}$。显然时间复杂度 $O(n^3)$ 是过不了的。

我们发现，因为是求两点路径中使得最小值最大，实际上有一些较小的路径是不会走到的，只是需要走那些值较大的路径。于是我们可以考虑将其删掉，只保留那些较大的路线，于是考虑建最大生成树。

建好树后，考虑对于每次询问如何处理，我们发现树上两点的路径是唯一的，对于两点，我们可以通过求 LCA 计算答案，两点树上倍增求 LCA，过程中计算答案即可。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int _ = 1e4 + 5, __ = 20, inf = 1e9;
int n, m, q;
struct node {
  int to/*x*/, nxt/*y*/, w/*dis*/;
} e1[5 * _], e2[5 * _]; int cnt, head[_];
// e1原始图，e2最大生成树
void add2(int u, int v, int w) { // 链式前向星存图
  e2[++cnt] = {v, head[u], w};
  head[u] = cnt;
}
bool cmp(node x, node y) { return x.w > y.w; }
int f[_];
int getfa(int x) { // 并查集
  if(f[x] == x) return x;
  return f[x] = getfa(f[x]);
}
void kruskal() { // kruskal 求最大生成树
  sort(e1 + 1, e1 + m + 1, cmp);
  for(int i = 1; i <= n; ++i) f[i] = i;
  for(int i = 1; i <= m; ++i)
    if(getfa(e1[i].to) != getfa(e1[i].nxt))
      f[getfa(e1[i].to)] = getfa(e1[i].nxt),
      add2(e1[i].to, e1[i].nxt, e1[i].w), add2(e1[i].nxt, e1[i].to, e1[i].w); // 建树
}
int fa[_][__ + 5], dis[_][__ + 5], dep[_]; bool vis[_];
// dis 树上倍增求值
void init() { // LCA 预处理
  for(int k = 1; k <= __; ++k)
    for(int i = 1; i <= n; ++i)
      fa[i][k] = fa[fa[i][k - 1]][k - 1],
      dis[i][k] = min(dis[i][k - 1], dis[fa[i][k - 1]][k - 1]);
}
void dfs(int u) {
  vis[u] = true;
  for(int i = head[u]; i; i = e2[i].nxt) {
    int v = e2[i].to;
    if(!vis[v]) fa[v][0] = u, dis[v][0] = e2[i].w, dep[v] = dep[u] + 1, dfs(v);
  }
}
int lca(int x, int y) { // 倍增求 LCA
  int res = inf;
  if(getfa(x) != getfa(y)) return -1; // 如果两点不连通
  if(dep[x] > dep[y]) swap(x, y);
  for(int i = __; i >= 0; --i)
    if(dep[fa[y][i]] >= dep[x])
      res = min(res, dis[y][i]), y = fa[y][i]; // 过程中维护答案
  if(x == y) return res;
  for(int i = __; i >= 0; --i)
    if(fa[x][i] != fa[y][i])
      res = min(res, min(dis[x][i], dis[y][i])),
      x = fa[x][i], y = fa[y][i];
  res = min(res, min(dis[x][0], dis[y][0])); // 处理 LCA 处答案
  return res;
}
void solve() {
  int u, v; cin >> u >> v;  
  cout << lca(u, v) << endl;
}
int main() {
  cin >> n >> m;
  for(int i = 1, u, v, w; i <= m; ++i)
    cin >> u >> v >> w, e1[i] = {u, v, w};
  kruskal();
  for(int i = 1; i <= n; ++i)
    if(!vis[i])
      dep[i] = 1, dfs(i), fa[i][0] = i, dis[i][0] = inf;
  init();
  cin >> q; while(q--) solve();
  return 0;
}
```