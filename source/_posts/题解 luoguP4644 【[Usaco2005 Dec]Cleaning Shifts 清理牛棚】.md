---
title: 题解 luoguP4644 【[Usaco2005 Dec]Cleaning Shifts 清理牛棚】
date: 2019-05-28 14:30:27
top: 1
tag: 
- 题解
- Luogu
- 网络流
- 费用流
---
[传送门](https://www.luogu.org/problemnew/show/P4644)

题解全是线段树啊？其实网络流也能过！！！！$($可能最近网络流题做太多了$)$

复杂度显然是不对的(逃

我们以天数为点,然后第$i$天向第$i+1$天连$INF-1$ $($第$i$天需要人数$)$容量，$0$价值的边

源点向第一天，最后一天$+1$天向汇点连$INF$容量，$0$价值的边

然后对于每个工作天数从$s-t$的奶牛，我们从第$s$天向第$t+1$天连$INF$容量，$c$价值的边

这张图怎么理解呢？

首先这张图的最大流肯定是$INF$

中间第$i$天向第$i+1$天连$INF-1$容量的边，流量少了$1$，就需要靠奶牛来补齐

一头奶牛可以给$s-t$路径上的所有边补上$1$的流量

我们要计算的就是获得最大流$INF$所需要的最小费用

即跑一遍最小费用最大流

但写完发现我们$T$飞了$($可能拿到$40$到$60$分不等$)$。

怎么办？众所周知$USACO$很能卡$SPFA$，那我们考虑优化$SPFA$。先加个$SLF$优化，发现$90$分了。再加$LLL$优化，开$O2$，卡过！

其实这道题和[[NOI2008]志愿者招募](https://www.luogu.org/problemnew/show/P3980)做法是一样的。

只不过这题数据大一点。

```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
#define time TTTT
using namespace std;
int n,m,st,ed,head[500005],vis[500005],time,dis[500005];
int cnt=1,ans,stt,edt;
int cost;
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
    deque<int> q;
    int tot=1,sum=0;
    q.push_back(st);
    while(!q.empty()){
        int now=q.front();
        while(tot*dis[now]>sum){
            q.pop_front();
            q.push_back(now);
            now=q.front();
        }
        q.pop_front();
        tot--;
        sum-=dis[now];
        vis[now]=0;
        for(int i=head[now];i;i=e[i].nx){
            int v=e[i].v;
            if(dis[v]>dis[now]+e[i].val&&e[i].s){
                dis[v]=dis[now]+e[i].val;
                if(!vis[v]){
                    vis[v]=1;
                    if(dis[v]>q.front()) q.push_back(v);
                    else q.push_front(v);
                    sum+=dis[v];
                    tot++;
                }
            }
        }
    }
    return dis[ed]!=1e9;
}
int dfs(int now,int ma){
    if(now==ed) return ans+=ma,ma;
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
    n=read(),stt=read(),edt=read();
    st=stt-1,ed=edt+2;
    for(int i=stt;i<=edt;i++){
        add(i,i+1,0,1e9-1);
        add(i+1,i,0,0);
    }
    add(st,stt,0,1e9);
    add(stt,st,0,0);
    add(edt+1,ed,0,1e9);
    add(ed,edt+1,0,0);
    for(int i=1;i<=n;i++){
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
    if(ans!=1e9) printf("-1");
    else printf("%d",cost);
    return 0;
}
```