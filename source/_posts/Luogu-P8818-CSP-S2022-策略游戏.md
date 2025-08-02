---
title: Luogu[P8818] [CSP-S2022] 策略游戏
date: 2023-05-13 22:22:41
tags:
    - 贪心
    - ST表
    - Luogu
    - "CSP-S"
categories: 题目解析
---


一道简单区间rmq分类讨论题，考场上最后5分钟想出来，没写出来，退役了……

<!-- more -->

给定两个序列 $A_{1},\dots,A_{n}$；$B_1,\dots,B_n$ 规定 $C_{i,j}=A_i\times B_i$。  
题目说小 L 和小 Q 必定选择最优策略，而小 L 先选，小 Q 后选，小 L 要使得 $C_{i,j}$ 尽可能大，小 Q 要使得 $C_{i,j}$ 尽可能小。  
共 $q$ 个询问。

### 考虑暴力

预处理出 $C_{i,j}$，对于每个询问，枚举 $ C_{l_1,r_1}  \sim C_{l_2,r_2} $ ，根据最优策略，小 L 会先找出一行，使得这一行中的最小值最大，使小 Q 所选出的值尽量不优。答案即为这一行中的最小值。

时间 $O(qnm)$  
空间 $O(nm)$

### 考虑优化

仍然预处理出 $C_{i,j}$。  
根据暴力，我们发现对于小 L 要找的那一行，我们需要每一行的区间最小值，于是不难想到，先预处理出每一行的最小值，然后对于每一个询问，枚举 $l_1 \sim l_2$ 对于每一行，查询区间 $ r_1 \sim r_2 $ 的最小值，找到一行使得最小值最大，答案即为这一行中的最小值。

时间 $O(qn)$  
空间 $O(nm)$

### 考虑再次优化

对于暴力空间 $O(nm)$ 我们知道一定无法通过本题，故我们需要不通过预处理 $C$ 来进行求解。

观察特殊性质我们发现有特殊性质 1 保证 $A_i,B_i>0$ 即保证其为正整数，对 $A_i,B_i$ 的正负号进行了规定，这也提示了我们，观察题目所给的 $C_{i,j} = A_i \times B_j$，对于乘法我们知道，正正得正，负负得负，正负得负，零乘任意数得零。于是我们可以对 $A_i,B_i$ 的正负号进行讨论。

对于当小 L 所选区域 $A_{l_1}\sim A_{l_2}$ 中：

1. $A_i$ 均为非负数
2. $A_i$ 均为非正数
3. $A_i$ 有正有负有零

则小 Q 所选区域 $B_{r_1}\sim B_{r_2}$ 同时也有：

1. $B_i$ 均为非负数
2. $B_i$ 均为非正数
3. $B_i$ 有正有负有零

我们进行分类讨论。

对于小 Q 第一种情况，无论小 L 所选数的正负，都要让该数尽可能大，这样 $A_i \times B_i$ 才能尽可能大，使得自己分数尽可能大；而当小 L 选非负数时，小 Q 则需要选最小的非负数使得乘积尽可能小，自己得分尽可能大；而当小 L 选非正数时，小 Q 则需要选最大的非负数使得乘积尽可能小。则小 L 需要考虑对于小 Q 的两种选取情况哪一种能使值最大，自己的得分尽可能大，故值即为两种情况的最大值。

对于小 Q 第二种情况，无论小 L 所选数的正负，都要让该数尽可能小，这样 $A_i \times B_i$ 才能尽可能大，使得自己分数尽可能大；而当小 L 选非负数时，小 Q 则需要选最小的非正数使得乘积尽可能小，自己得分尽可能大；而当小 L 选非正数时，小 Q 则需要选最大的非负数使得乘积尽可能小。则小 L 需要考虑对于小 Q 的两种选取情况哪一种能使值最大，自己的得分尽可能大，故值即为两种情况的最大值。  

对于小 Q 第三种情况，  
若小 L 可选非正数，则小 Q 必选最大的非负数使得乘积为负且最小，故小 L 需要让该非正数尽可能大，使得乘积尽可能大，自己得分尽可能大。所以小 L 选非正数最大，小 Q 选非负数最大；  
若小 L 可选非负数，则小 Q 必选最小的非正数使得乘积为负且最小，故小 L 需要让该非负数尽可能小，使得乘积尽可能大，自己得分尽可能大，所以小 L 选非负数最小，小 Q 选非正数最小。  
故小 L 需要比较小 Q 的两种选取方式哪个值更大，最终答案即为两值中更大的。

这样，我们就无需预处理 $C$，仅对每次询问的 $A,B$ 进行分来讨论即可求得答案。

对于我们要查询的 $A$ 中非正数最大、最小，非负数最大、最小；$B$ 中非正数最大、最小，非负数最大、最小，可以用 st 表或线段树轻松预处理出来。

我选择 st 表，用 $O(n \log n)$ 预处理，对于每个询问都 $O(1)$ 查询。  
时间 $O(n\log n+q)$

本题于是就做完了。

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int NR=1e5+5;
const int KR=50;
const int MX=1e9+5;
const int MN=-1e9-5;
#define rep(x) for(int i=1;i<=x;++i)
#define repi(x) for(int i=1;i+(1<<k)-1<=x;++i)
#define repk(x) for(int k=1;(1<<k)<=x;++k)
int n,m,q;
int a[NR],b[NR];
//g开头即维护的是非正数，f开头即维护的是非负数
//x结尾即维护最大值，n结尾即维护最小值
int fax[NR][KR],fan[NR][KR],gax[NR][KR],gan[NR][KR];//序列a的
int fbx[NR][KR],fbn[NR][KR],gbx[NR][KR],gbn[NR][KR];//序列b的
struct Q{
    int fax,fan,gax,gan;
    int fbx,fbn,gbx,gbn;
};
void init(){//维护st表
    rep(n)
        repk(n)
            fax[i][k]=gax[i][k]=MN,fan[i][k]=gan[i][k]=MX;
    rep(m)
        repk(m)
            fbx[i][k]=gbx[i][k]=MN,fbn[i][k]=gbn[i][k]=MX;
    repk(n)
        repi(n)
            fax[i][k]=max(fax[i][k-1],fax[i+(1<<k-1)][k-1]),
            gax[i][k]=max(gax[i][k-1],gax[i+(1<<k-1)][k-1]),
            fan[i][k]=min(fan[i][k-1],fan[i+(1<<k-1)][k-1]),
            gan[i][k]=min(gan[i][k-1],gan[i+(1<<k-1)][k-1]);
    repk(m)
        repi(m)
            fbx[i][k]=max(fbx[i][k-1],fbx[i+(1<<k-1)][k-1]),
            gbx[i][k]=max(gbx[i][k-1],gbx[i+(1<<k-1)][k-1]),
            fbn[i][k]=min(fbn[i][k-1],fbn[i+(1<<k-1)][k-1]),
            gbn[i][k]=min(gbn[i][k-1],gbn[i+(1<<k-1)][k-1]);
}
int query(int F[NR][KR],int L,int R,int B){
    int K=log2(R-L+1);
    if(B==1)return max(F[L][K],F[R-(1<<K)+1][K]);
    return min(F[L][K],F[R-(1<<K)+1][K]);
}
signed main(){
    cin>>n>>m>>q;
    for(int i=1;i<=n;++i){
        cin>>a[i];
        fax[i][0]=gax[i][0]=MN;
        fan[i][0]=gan[i][0]=MX;
        if(a[i]>=0)fax[i][0]=fan[i][0]=a[i];
        if(a[i]<=0)gax[i][0]=gan[i][0]=a[i];
    }
    for(int i=1;i<=m;++i){
        cin>>b[i];
        fbx[i][0]=gbx[i][0]=MN;
        fbn[i][0]=gbn[i][0]=MX;
        if(b[i]>=0)fbx[i][0]=fbn[i][0]=b[i];
        if(b[i]<=0)gbx[i][0]=gbn[i][0]=b[i];
    }
    init();
    while(q--){
        int lL,lR,qL,qR,ans=-1e18-5;
        cin>>lL>>lR>>qL>>qR;
        Q res;
        res.fax=query(fax,lL,lR,1),res.fan=query(fan,lL,lR,-1);
        res.gax=query(gax,lL,lR,1),res.gan=query(gan,lL,lR,-1);
        res.fbx=query(fbx,qL,qR,1),res.fbn=query(fbn,qL,qR,-1);
        res.gbx=query(gbx,qL,qR,1),res.gbn=query(gbn,qL,qR,-1);
        if(res.fbn!=MX&&res.gbx!=MN){//小 Q 有非正有非负
            if(res.gax!=MN)//小 L 可以选非正数
                ans=max(ans,res.gax*res.fbx);
            if(res.fan!=MX)//小 L 可以选非负数
                ans=max(ans,res.fan*res.gbn);
            // cout<<"-1-"<<endl;
        }
        else if(res.gbx!=MN&&res.fbx==MN){//小 Q 只有非正
            if(res.gan!=MX)//小 L 可以选非正数
                ans=max(ans,res.gan*res.gbx);
            if(res.fan!=MX)//小 L 可以选非负数
                ans=max(ans,res.fan*res.gbn);
            // cout<<"-2-"<<endl;
        }
        else if(res.fbn!=MX&&res.gbn==MX){//小 Q 只有非负
            if(res.fax!=MN)//小 L 可以选非负数
                ans=max(ans,res.fax*res.fbn);
            if(res.gax!=MN)//小 L 可以选非正数
                ans=max(ans,res.gax*res.fbx);
            // cout<<"-3-"<<endl;
        }
        std::cout<<ans<<endl;
    }
    return 0;
}
```

以此，纪念初中最后一次 CSP。以此，纪念我那美好的信竞生涯。