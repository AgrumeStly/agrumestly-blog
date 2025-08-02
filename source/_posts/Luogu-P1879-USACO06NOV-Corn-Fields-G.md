---
title: Luogu[P1879] [USACO06NOV] Corn Fields G
date: 2023-05-13 22:08:39
tags:
    - 状态压缩
    - 状压dp
    - 轮廓线dp
    - dp动态规划
    - Luogu
    - USACO
categories: 题目解析
---

[Link→](https://www.luogu.com.cn/problem/P1879)

状压dp典题，看数据范围就能多半猜到是状压。

<!--more-->

$M$ 行 $N$ 列很不舒服，本篇题解规定为 $N$ 行 $M$ 列。

因为说没有哪两块草地相连，我们不妨一行一行考虑，一行中每格只可能是 $0$ 或 $1$，所以一行的总不同状态数是 $2^M$。我们用二进制表示每一行的状态，对于每一行，暴力枚举当前行和上一行的每一种状态。

我们用 $f[i]$ 表示第 $i$ 行的状态，$g[i]$ 表示状态为 $i$ 时是否符合题意。$dp[i][j]$ 表示到第 $i$ 行，状态为 $j$ 时的方案总数。

对于预处理 $g[i]$，题目规定两块草地不能有公共边，故没有两块草地相连，当前状态 $i$ 与向左平移一格和向右平移一格后的状态，均没有重合的 $1$，即 `!(i&(i<<1))&&!(i&(i>>1))$`。

而对于第 $i$ 行，当前状态 $j$，仅当它在一行中没有相邻的草地，并且没有草种在贫瘠的土地上，即 `g[j]&&(j&f[i])==j`，才可转移。

枚举上一行的状态 $k$，确保上下两行没有相邻的草地，即 `!(k&j)`。转移方程显而易见 $dp_{i,j}=dp_{i,j}+dp_{i-1,k}$。

```cpp
#include<bits/stdc++.h>
using namespace std;
const int _=20, mod=1e8;
int n,m; int a[_][_];
int f[_],dp[_][1<<_]; bool g[1<<_];
int ans;
int main() {
    ios::sync_with_stdio(false); cin.tie(0),cout.tie(0);
    cin>>n>>m;
    for(int i=1;i<=n;++i)
        for(int j=1;j<=m;++j)
            cin>>a[i][j],f[i]=(f[i]<<1)+a[i][j];
    m=(1<<m),dp[0][0]=1;
    for(int i=0;i<m;++i)
        g[i]=!(i&(i<<1))&&!(i&(i>>1));
    for(int i=1;i<=n;++i)
        for(int j=0;j<m;++j)
            if(g[j]&&(j&f[i])==j)
                for(int k=0;k<m;++k)
                    if(!(k&j))
                        dp[i][j]=(dp[i][j]+dp[i-1][k])%mod;
    for(int i=0;i<m;++i)
        ans=(ans+dp[n][i])%mod;
    cout<<ans<<endl;
    return 0;
}
```