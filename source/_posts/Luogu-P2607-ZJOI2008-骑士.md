---
title: Luogu[P2607] [ZJOI2008] 骑士
date: 2023-07-19 23:13:56
tags:
    - dp动态规划
    - 树形dp
    - Luogu
    - 省选
categories: 题目解析
---

[Link](https://www.luogu.com.cn/problem/P2607)

题目说给定 $n$ 个点 $n$ 个关系，也就是 $n$ 条边，显然是基环树，又因为没有规定一定连通，于是我们可以将题目简化为给定一个基环树森林，点有点权，相邻的两个点不能同时选，问最大点权和。

<!-- more -->

### part1

我们先考虑如果没有环，只是树，该怎么做。

这一部分很简单，令 $f_{i,0/1}$ 表示以 $i$ 为根的子树内，且 $i$ 号点选或不选的最大点权和。$u$ 为父节点 $v$ 为子节点，显然有 $f_{u,0}=\sum\max(f_{v,1}, f_{v,0})\quad f_{u,1}=\sum\max(f_{v,0})$

### part2

考虑环，如果只有环，该怎么做。

也很简单，与树类似令 $f_{i,0/1}$ 表示前 $i$ 个点，且第 $i$ 个点选或不选的最大点权和。但是此时有个问题就是首尾不能同时选，那么我们不妨强制首选与强制尾选分别做一遍 dp 即可。

### part3

现在考虑基环树怎么做。我们会了树怎么做，也会了环怎么做，将他们融合起来。我们知道在环上删除一条边，必能成为一棵树，不妨利用破环为链的思想，将环上一边删去，将其转化为树，于是问题转化为了 part1。

不过要注意断掉的边的两点不能同时选，于是我们利用 part2 的方法，强制两点选和不选，分别做一次 dp 即可。

### code

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int NR = 1e6 + 5;
const ll MX = 1e18;
int n, a[NR], to[NR];
vector< int > e[NR];
bool vis[NR];
ll root, f[NR][3], ans;
void dp(int x) {
	vis[x] = true, f[x][0] = 0, f[x][1] = a[x];
	for(int i = 0; i < e[x].size(); ++i) {
		int v = e[x][i];
		if(v != root) dp(v), f[x][0] += max(f[v][0], f[v][1]), f[x][1] += f[v][0];
		else f[v][1] = -MX;
	}
} 
void find_circle(int x) {
	vis[x] = true;
	while(!vis[to[x]]) x = to[x], vis[x] = true;
	root = x, dp(x);
	ll num = max(f[x][0], f[x][1]);
	x = root = to[x], dp(x);
	ans += max(num, max(f[x][0], f[x][1]));
}
signed main() {
	ios::sync_with_stdio(false); cin.tie(0), cout.tie(0);
	cin >> n;
	for(int i = 1; i <= n; ++i)
		cin >> a[i] >> to[i], e[to[i]].push_back(i);
	for(int i = 1; i <= n; ++i)
		if(!vis[i]) find_circle(i);
	cout << ans << endl;
	return 0;
}
```