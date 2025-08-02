---
title: Luogu[P4427] [BJOI2018]求和
date: 2023-05-18 21:37:10
tags:
    - 倍增
    - LCA最近公共祖先
    - 省选
    - Luogu
categories: 题目解析
---

先思考对于 $k=1$ 我们怎么做？我们令 $dk_i$ 表示根节点到 $i$ 号节点的深度和， $dep_i$ 表示 $i$ 号节点的深度，那么对于路径 $i,j$ 的深度和就是 $dk_i + dk_j - 2 \times dk_{lca(i,j)} + dep_{lca(i,j)}$。

<!-- more -->

那么我们思考加上次方后怎么做，发现 $k$ 很小，只有 $50$，于是我们不妨令 $dk_{i, k}$ 表示根节点到 $i$ 号节点路径上所有深度的 $k$ 次方和。那么对于路径 $i,j$ 的 $k$ 次方深度和与前面一样，就是 $dk_{i,k} + dk_{j,k} - 2 \times dk_{lca(i,j), k} + dep_{lca(i,j)}^{k}$。

于是我们思考如何预处理 $dk$，类似于前缀和，不难推出 $dk_{i,k} = dk_{fa(i),k} + dep_{i}^k$，而 $dep_{i}^k$ 我们可以使用快速幂。

最终时间复杂度 $O(n \log n + m \log n)$

```cpp
#include <bits/stdc++.h>
using namespace std;
#define int long long
const int _ = 3e5 + 5;
const int mod = 998244353;
int n, m;
vector< int > e[_];
int dep[_], fa[_][20], lg[_];
int dek[_], dk[_][60];
int qpow(int x, int y) {
	if (y <= 1) return x % mod;
	int t = qpow(x, y / 2) % mod;
	if (y % 2) return x * t % mod * t % mod;
	return t % mod * t % mod;
}
void init() {
	for(int i = 1; i <= n; ++i)
		lg[i] = lg[i - 1] + (1 << lg[i - 1] == i);
}
void dfs(int now, int fath) {
	dep[now] = dep[fath] + 1, fa[now][0] = fath;
	dek[now] = dek[fath] + 1;
	for(int i = 1; i <= 50; ++i)
		dk[now][i] = (dk[fath][i] % mod + qpow(dek[now], i) % mod) % mod;
	for(int i = 1; i <= lg[dep[now]]; ++i)
		fa[now][i] = fa[fa[now][i - 1]][i - 1];
	for(int i = 0; i < e[now].size(); ++i)
		if(e[now][i] != fath) dfs(e[now][i], now);
}
int lca(int x, int y) {
	if(dep[x] < dep[y]) swap(x, y);
	while(dep[x] > dep[y]) x = fa[x][lg[dep[x] - dep[y]] - 1];
	if(x == y) return x;
	for(int k = lg[dep[x]] - 1; k >= 0; --k)
	if(fa[x][k] != fa[y][k]) x = fa[x][k], y = fa[y][k];
	return fa[x][0];
}
void solve() {
	int u, v, k;
	cin >> u >> v >> k;
	int _lca = lca(u, v);
	int ans = ((dk[u][k] + dk[v][k]) % mod - 2 * dk[_lca][k] % mod + qpow(dek[_lca], k) % mod + mod) % mod;
	cout << ans << endl;
}
signed main() {
	cin >> n;
	for(int i = 1, u, v; i < n; ++i)
		cin >> u >> v, e[u].push_back(v), e[v].push_back(u);
	dek[0] = -1;
	init(), dfs(1, 0);
	cin >> m;
	while(m--) solve();
	return 0;
}
```