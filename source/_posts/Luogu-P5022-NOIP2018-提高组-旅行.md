---
title: Luogu[P5022] [NOIP2018 提高组] 旅行
date: 2023-08-03 12:43:53
tags:
    - dfs深度优先搜索
    - 贪心
    - 基环树
    - Luogu
    - NOIP
categories: 题目解析
---

[Link](https://www.luogu.com.cn/problem/P5022)

因为是道NOIP，那么我们不妨按照考场上的策略一点一点想。

<!-- more -->

先看部分分，有一档有很明显的特征 $n=m-1$ 这显然构成一棵树，对于一棵树，我们想把他按照题目的要求遍历完，一定是像dfs的遍历顺序一样，对于一个点，必然遍历完以它为根的子树，才能回到它的父亲节点，于是就有了一个很明显的贪心策略，我们从节点 $1$ 开始遍历，对于一个点 $x$，我们按照从小到大的顺序去遍历它的儿子，因为是字典序最小，于是我们很容易证明这种“步步为赢”的策略是对的。

于是 $60pts$ 我们就到手了，在考场上我们就需要考试是否还需要去想剩下的 $40pts$，正确选择去写正解还是部分分，体现的就是智慧了。

剩下 $40pts$ 同样有很明显的特征 $n=m$ 这显然就是一个基环树，树的情形我们已经会做，那么此时我首先就会思考是否能把基环树的情形转化成我们已经会了的树。

一开始我想的是先找环，对于一个节点 $x$，若其儿子在环上，用贪心策略通过断环为链的方式转化成树，但是仔细思考发现有些难以实现。

我们知道基环树删掉环上一边，其必然成一棵树，再一看数据范围发现 $n,m$ 都比较小，很明显是个 $O(n^2)$ 的算法，于是我们就可以暴力枚举每一条边，删掉这条边，于是转化为了树，然后按照之前 $60pts$ 同样的方法求得答案，再在众多答案中取最小值，于是完美的通过了这道题，时间复杂度 $O(n^2)$

整体来说比较简单，考场上属于送分题。


```cpp
#include<bits/stdc++.h>
using namespace std;
const int NR=5005;
int n,m;
vector<int>e[NR],ans;
int U[NR],V[NR];
namespace s1{//60pts
    bool vis[NR];int res[NR],cnt;
    void dfs(int u){
        ans.push_back(u),vis[u]=true;
        for(int i=0;i<e[u].size();++i){
            int v=e[u][i];
            if(!vis[v])dfs(v);
        }
    }
    void solve(){
        dfs(1);
        for(int i=0;i<ans.size();++i)
            cout<<ans[i]<<" ";
    }
}
namespace s2{//100pts
    bool vis[NR];int nu,nv,step;
    int res[NR],ans[NR];
    bool cmp(){
        for(int i=1;i<=n;++i)
            if(res[i]<ans[i])return true;
            else if(res[i]>ans[i])return false;
        return false;
    }
    bool chk(int u,int v){
        if(u==nu&&v==nv||u==nv&&v==nu)return false;
        return true;
    }
    void dfs(int u){
        vis[u]=true,res[++step]=u;
        for(int i=0;i<e[u].size();++i){
            int v=e[u][i];
            if(!vis[v]&&chk(u,v))dfs(v);
        }
    }
    void solve(){
        for(int i=1;i<=m;++i){
            int u=U[i],v=V[i];
            memset(res,0,sizeof(res)),memset(vis,false,sizeof(vis)),step=0;
            nu=u,nv=v,dfs(1);
            if(step<n)continue;//若删掉的这条边不在环上，无法成为连通图，就跳过。
            if(ans[1]==0||cmp())memcpy(ans,res,sizeof(res));
        }
        for(int i=1;i<=n;++i)
            cout<<ans[i]<<" ";
    }
}
int main(){
    cin>>n>>m;
    for(int i=1,u,v;i<=m;++i)
        cin>>u>>v,e[u].push_back(v),e[v].push_back(u),U[i]=u,V[i]=v;
    for(int i=1;i<=n;++i)
        sort(e[i].begin(),e[i].end());
    if(m==n-1)s1::solve();
    else if(m==n)s2::solve();
    return 0;
}
```