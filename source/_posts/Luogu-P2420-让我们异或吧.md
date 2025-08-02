---
title: Luogu[P2420] 让我们异或吧
date: 2023-08-01 15:35:55
tags:
    - 数学
    - dfs深度优先搜索
    - Luogu
categories: 题目解析
---

[Link](https://www.luogu.com.cn/problem/P2420)

看到是树，又多组询问，立马想到类似的求和问题，异或不好理解，我们想求和怎么做，维护 $dis_i$ 表示 $i$ 节点到根的权值和，那么对于 $u,v$ 两点路径上的权值和就是 $dis_u+dis_v-2\times dis_{\mathrm{lca}(u,v)}$，这是很经典的问题了。

<!-- more -->

事实上刚才的求和我们可以转化一下，我们令 $dis_{x\sim y}$ 表示 $x$ 到 $y$ 路径上的权值和，它的本质实际上是 $dis_{u\sim \mathrm{lca}(u,v)}+dis_{\mathrm{lca}(u,v)\sim \mathrm{root}}+dis_{v\sim \mathrm{lca}(u,v)}+dis_{\mathrm{lca}(u,v)\sim \mathrm{root}}$ 然后因为多加了两个 $dis_{\mathrm{lca}(u,v)\sim \mathrm{root}}$ 于是我们把他删掉。

现在回到异或，类似的，维护 $dis_i$ 表示 $i$ 节点到根的异或，相同的转化，$dis_{u\sim \mathrm{lca}(u,v)}\oplus dis_{\mathrm{lca}(u,v)\sim \mathrm{root}}\oplus dis_{v\sim \mathrm{lca}(u,v)}\oplus dis_{\mathrm{lca}(u,v)\sim \mathrm{root}}$，这时我们发现根本不需要处理多出来的 $dis_{\mathrm{lca}(u,v)\sim \mathrm{root}}\oplus dis_{\mathrm{lca}(u,v)\sim \mathrm{root}}$！

为什么？因为根据小学知识我们知道 $A\oplus A=0$，而 $A\oplus 0=A$，多余的部分对我们的答案没有影响，于是对于每个询问，我们的答案就是 $dis_u\oplus dis_v$

### code

```cpp
#include<bits/stdc++.h>
using namespace std;
const int NR=1e5+5;
int n,m;
vector<int>e[NR],g[NR];
int dis[NR];bool vis[NR];
void add(int u,int v,int w){
    e[u].push_back(v),g[u].push_back(w);
}
void dfs(int u,int val){
    dis[u]=val,vis[u]=true;
    for(int i=0;i<e[u].size();++i){
        int v=e[u][i],w=g[u][i];
        if(!vis[v])dfs(v,val^w);
    }
}
int main(){
    cin>>n;
    for(int i=1,u,v,w;i<n;++i)
        cin>>u>>v>>w,add(u,v,w),add(v,u,w);
    dfs(1,0);
    cin>>m;
    for(int i=1,u,v;i<=m;++i)
        cin>>u>>v,cout<<(dis[u]^dis[v])<<endl;
    return 0;
}
```