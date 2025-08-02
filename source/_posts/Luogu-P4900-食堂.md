---
title: Luogu[P4900] 食堂
date: 2023-05-13 22:20:01
tags:
    - 前缀和
    - 逆元
    - 素数判断、质数、筛法
    - Luogu
categories: 题目解析
---


一到推柿子题。

<!-- more -->

题意即求 $\sum\limits^{n}_{i=1}\sum\limits^{i}_{j=1}\left\{\frac{i}{j}\bmod P\right\}$

对于 $\frac{i}{j}\bmod P$ 我们知道即为 $i$ 乘上 $j$ 的逆元，即为 $i\cdot\mathrm{inv}(j)$

故：

$$
\begin{aligned}
\sum^{n}_{i=1}\sum^{i}_{j=1}
\left\{\frac{i}{j}\right\} 
    &=\sum^{n}_{i=1}\sum^{i}_{j=1}\left\{\frac{i}{j}\bmod P-\left\lfloor\frac{i}{j}\right\rfloor
\right\}\\
    &=\sum^{n}_{i=1}\sum^{i}_{j=1}i\cdot\mathrm{inv}(j)-\sum^{n}_{i=1}\sum^{i}_{j=1}\left\lfloor\frac{i}{j}\right\rfloor\\
    &=\sum^{n}_{i=1}\left[i\left(\sum^{i}_{j=1}\mathrm{inv}(j)\right)\right]-\sum^{n}_{i=1}\sum^{i}_{j=1}\left\lfloor\frac{i}{j}\right\rfloor\\
\end{aligned}
$$

其中 $\{x\}$ 表示 $x$ 的小数部分，$\mathrm{inv}(x)$ 表示 $x$ 的逆元。

对于 $\sum\limits^{n}_{i=1}\left[i\left(\sum\limits^{i}_{j=1}\mathrm{inv}(j)\right)\right]$ 通过前缀和处理。

对于 $\mathrm{inv}(x)$，$\mathrm{inv}(i)=\mathrm{inv}(P\bmod i)\cdot(P-\left\lfloor\frac{P}{i}\right\rfloor)\bmod P$，$\mathrm{inv}(1)=1$

对于 $\sum\limits^{n}_{i=1}\sum\limits^{i}_{j=1}\left\lfloor\frac{i}{j}\right\rfloor$，考虑 $\sum\limits^{n}_{i=1}\sum\limits^{n}_{j=1}\left\lfloor\frac{i}{j}\right\rfloor$ 所求即为 $1\sim i$ 之间有多少 $j$ 的倍数。定义 $cnt_i$ 表示 $i$ 恰好是几个数的倍数，通过筛法求出 $cnt$，故最后值即为 $cnt_1+cnt_2+\dots+cnt_i$

最后答案即为两部分的差。

预处理 $O(n\log n)$ 询问 $O(1)$。

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int NR=1e6+5;
const int P=998244353;
int inv[NR],sumi[NR],sum1[NR],sum2[NR],cnt[NR];
void init(){
    inv[1]=1;
    for(int i=2;i<=NR;++i)
        inv[i]=inv[P%i]%P*(P-P/i)%P;
    for(int i=1;i<=NR;++i)
        sumi[i]=(sumi[i-1]+inv[i])%P;
    sum1[1]=1;
    for(int i=2;i<=NR;++i)
        sum1[i]=(i*sumi[i])%P;
    for(int i=1;i<=NR;++i)
        sum1[i]=(sum1[i-1]+sum1[i])%P;
    for(int i=1;i<=NR;++i)
        for(int j=i;j<=NR;j+=i)
            cnt[j]++;
    for(int i=1;i<=NR;++i)
        sum2[i]=(sum2[i-1]+cnt[i])%P;
    for(int i=1;i<=NR;++i)
        sum2[i]=(sum2[i-1]+sum2[i])%P;
}
void solve(){
    int a,b;
    cin>>a>>b;
    cout<<((sum1[b]-sum1[a-1]+P)%P-(sum2[b]-sum2[a-1]+P)%P+P)%P<<endl;
}
signed main(){
    ios::sync_with_stdio(false);cin.tie(0),cout.tie(0);
    init();
    int t;
    cin>>t;
    while(t--)solve();    
    return 0;
}
```