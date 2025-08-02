---
title: Luogu[P9504] 『MGOI』Simple Round I  C. 魔法禁林
date: 2023-08-06 20:14:00
tags:
    - 图论
    - 最短路
    - 分层图
    - Luogu
categories: 题目解析
---

[Link](https://www.luogu.com.cn/problem/P9504)

这题我们发现如果直接去枚举生命和法力值显然是不行的，又看到说最小的生命值，不禁想到最短路，但是怎么跑？

<!-- more -->

我们令经过一条边之前魔力值为 $k$，那么该边的边权为 $\lfloor\dfrac{w}{k}\rfloor$，于是我们讲题目转化为了边权为 $\lfloor\dfrac{w}{k}\rfloor$ 时 $s$ 到 $t$ 的最短路径。

我们不知道最短路径一共要经过多少条边，也就无法知道初始魔力值究竟有多少。那我们不妨反着想，考虑从 $t$ 到 $s$ 的最短路径，这时我们就不用知道初始魔力值是多少，而是每经过一个边，就将 $k\leftarrow k+1$。

但是此时仍无法直接跑最短路，因为每一次不同时刻经过一条边，他的边权都是不一样的。此时数据范围 $w\leq 100$ 很好的提示了我们。

妈妈，我会拆点分层图！

我们将原本的一个点拆成 $100$ 个点，因为 $w$ 最大为 $100$，若经过的边数（即魔力值）$k>100$，那么他的边权 $w$ 就必然为 $0$，我们就不需要考虑了，因为不会对我们的答案产生影响。

于是对于点 $x$，我们将其拆成 $100$ 个点 $x\times 100+i(1\leq i\leq 100)$，其中的 $i$ 表示在 $x\times 100+i$ 这个点时的魔力值为 $i$，于是显然的对于两个点 $u,v$，其边权为 $w$，若其在原始图上有边相连，那么我们就在分层图上将 $u\times 100 +i(1\leq i < 100)$ 与 $v\times 100 +i+1$ 相连，其边权为 $\lfloor\dfrac{w}{i}\rfloor$。

最后也就很简单了，我们在新的分层图上跑一遍 $t$（新图上为 $t\times 100 +1$） 到 $s$ 的最短路，最后在 $s$ 点的所有层里面找最小值即为最终答案。


```cpp
#include<bits/stdc++.h>
using namespace std;
const int NR=3e6;
const int MX=2e4;
int n,m,s,t;
vector<int>e[NR],g[NR];
void add(int u,int v,int w){
    e[u].push_back(v),g[u].push_back(w);
}
bool vis[NR];
int dis[NR],ans;
queue<int>q;
void spfa(int _s){
    memset(vis,false,sizeof(vis));
    memset(dis,0x3f3f3f3f,sizeof(dis));
    ans=dis[0];
    q.push(_s),vis[_s]=true,dis[_s]=0;
    while(!q.empty()){
        int u=q.front();q.pop();
        vis[u]=false;
        for(int i=0;i<e[u].size();++i){
            int v=e[u][i],w=g[u][i];
            if(dis[v]>dis[u]+w){
                dis[v]=dis[u]+w;
                if(!vis[v])q.push(v),vis[v]=true;
            }
        }
    }
}
int main(){
    cin>>n>>m>>s>>t;
    for(int i=1,u,v,w;i<=m;++i){
        cin>>u>>v>>w;
        for(int j=1;j<100;++j){
            add(u*100+j,v*100+j+1,w/j);
            add(v*100+j,u*100+j+1,w/j);
        }
    }
    spfa(t*100+1);
    for(int i=s*100+1;i<=s*100+100;++i)
        ans=min(ans,dis[i]);
    cout<<ans<<endl;
    return 0;
}
```