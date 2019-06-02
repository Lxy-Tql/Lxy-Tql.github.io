---
title: 题解 luoguP3980 【[NOI2008]志愿者招募】
date: 2019-05-28 14:30:27
top: 1
tag: 
- 题解
- Luogu
- 网络流
- 费用流
---
[传送门](https://www.luogu.org/problemnew/show/P3980)

~~极其神仙的一道题~~

神仙就神仙在一个人可以对一段区间造成贡献

解题用到的主要是很神仙的建图方法$($看了题解$)$

我们以天数为点,然后第$i$天向第$i+1$天连$INF-w[i]$ $($第$i$天需要人数$)$容量，$0$价值的边

源点向第一天，第$n+1$天向汇点连$INF$容量，$0$价值的边

然后对于每个工作天数从$s-t$的志愿者，我们从第$s$天向第$t+1$天连$INF$容量，$c$价值的边

这张图怎么理解呢？

首先这张图的最大流肯定是$INF$

中间第$i$天向第$i+1$天连$INF-w[i]$容量的边，流量少了$w[i]$，就需要靠志愿者来补齐

一个志愿者可以给$s-t$路径上的所有边补上$1$的流量

我们要计算的就是获得最大流$INF$所需要的最小费用

即跑一遍最小费用最大流
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
#define time TTTT
using namespace std;
int n,m,st,ed,head[20005],vis[20005],time,dis[20005],w[1005];
int cnt=1,cost,ans;
struct Edge{
    int v,nx,val,s;
}e[2000005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
inline void add(int x,int y,int val,int s){
    e[++cnt].v=y;
    e[cnt].val=val;
    e[cnt].s=s;
    e[cnt].nx=head[x];
    head[x]=cnt;
}
inline bool spfa(){
    for(int i=0;i<=ed;i++) vis[i]=0,dis[i]=1e9;
    dis[st]=0;
    queue<int> q;
    q.push(st);
    while(!q.empty()){
        int now=q.front();
        q.pop();
        vis[now]=0;
        for(int i=head[now];i;i=e[i].nx){
            int v=e[i].v;
            if(dis[v]>dis[now]+e[i].val&&e[i].s){
                dis[v]=dis[now]+e[i].val;
                if(!vis[v]){
                    vis[v]=1;
                    q.push(v);
                }
            }
        }
    }
    return dis[ed]!=1e9;
}
int dfs(int now,int ma){
    if(now==ed) return ma;
    vis[now]=time;
    int used=0,t;
    for(int i=head[now];i;i=e[i].nx){
        int v=e[i].v;
        if((v==ed||vis[v]!=time)&&e[i].s&&dis[v]==dis[now]+e[i].val){
            if(t=dfs(v,min(ma-used,e[i].s))){
                e[i].s-=t;
                e[i^1].s+=t;
                cost+=e[i].val*t;
                used+=t;
                if(used==ma) break;
            }
        }
    }
    return used;
}
signed main(){
    n=read(),m=read();
    st=0,ed=n+2;
    for(int i=1;i<=n;i++){
        int t=read();
        add(i,i+1,0,1e9-t);
        add(i+1,i,0,0);
    }
    add(st,1,0,1e9);
    add(1,st,0,0);
    add(n+1,ed,0,1e9);
    add(ed,n+1,0,0);
    for(int i=1;i<=m;i++){
        int s=read(),t=read(),c=read();
        add(s,t+1,c,1e9);
        add(t+1,s,-c,0);
    }
    while(spfa()){
        do{
            time++;
            dfs(st,1e9);
        }while(vis[ed]==time);
    }
    printf("%d",cost);
    return 0;
}
```