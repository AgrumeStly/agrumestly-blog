---
title: Luogu[P2296] [NOIP2014 提高组] 寻找道路
date: 2023-08-02 15:22:52
tags:
    - dfs深度优先搜索
    - 图论
    - 最短路
    - bfs广度优先搜索
    - Luogu
    - NOIP
categories: 题目解析
---

[Link](https://www.luogu.com.cn/problem/P2296)

很简单的一道图论题。

<!-- more -->

要在一个有向图上找一条 $s$ 到 $t$ 的最短路，要求这条路径上的所有点都满足：该点的所有出边所连点都能到达终点 $t$。

看上去很乱，我们简单分解一下，先在所有点中找到与终点有路径的点集 $A$ 进行标记，再再所有点中找到其所有出边所连点都被打上标记的点集 $B$。

很容易证明 $B\subseteq A$，因为对于任意一点 $x\in B$ 则说明 $x$ 必可到达与之相连的点 $y$，其中 $y\in A$，又因为 $y$ 可到达 $t$，所以 $x$ 必可到达 $t$，所以 $\forall x\in B,x\in A$。

于是思路就很明确了，先找到 $A$，再在 $A$ 中找 $B$，最后在 $B$ 中跑一遍最短路即可。这里因为边权都为 $1$，跑 01bfs 即可。

这里对于找 $A$，我们发现直接从起点开始找很难找，我们不妨再建一个反图，从终点开始找，就很好找了。

### code

```cpp
#include<bits/stdc++.h>
using namespace std;
const int NR=1e4+5;
int n,m;
vector<int>e[NR],g[NR];
int s,t;
bool ct[NR],lt[NR];
int dis[NR];
queue<int>q;
void dfs(int u){
    ct[u]=true;
    for(int i=0;i<e[u].size();++i){
        int v=e[u][i];
        if(!ct[v])dfs(v);
    }
}
void bfs(){
    dis[s]=1,q.push(s);
    while(!q.empty()){
        int u=q.front();q.pop();
        if(u==t){cout<<dis[t]-1;exit(0);}
        for(int i=0;i<g[u].size();++i){
            int v=g[u][i];
            if(lt[v]&&!dis[v])
                dis[v]=dis[u]+1,q.push(v);
        }
    }
}
int main(){
    cin>>n>>m;
    for(int i=1,u,v;i<=m;++i)
        cin>>u>>v,e[v].push_back(u),g[u].push_back(v);
    cin>>s>>t;
    ct[t]=true,dfs(t);
    if(!ct[s]){cout<<-1<<endl;return 0;}
    for(int i=1;i<=n;++i)
        if(ct[i]){
            lt[i]=true;
            for(int j=0;j<g[i].size();++j)
                if(!ct[g[i][j]]){lt[i]=false;break;}
        }
    if(!lt[s]){cout<<-1<<endl;return 0;}
    bfs();
    cout<<-1<<endl;
    return 0;
}
```