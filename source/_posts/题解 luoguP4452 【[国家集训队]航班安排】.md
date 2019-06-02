---
title: 题解 luoguP4452 【[国家集训队]航班安排】
date: 2019-06-02 16:20:37
top: 1
tag: 
- 题解
- luogu
- 网络流
- 费用流
---
[传送门](https://www.luogu.org/problemnew/show/P4452)

好像这道题并没有其他两篇题解说的那么简单吧？？？~~或者是我太菜了~~

考虑以请求为点进行建图，对每个请求进行拆点，拆点后两个点之间连价值为$c$，流量为$1$的边，代表着一个请求只能执行一次。

然后我们考虑时间限制：

对于一个请求，如果$0$时刻可以从$0$机场飞到该请求的起点机场，那么源点向该请求连价值为($-$飞行费用)，流量为$INF$的边，同理，若一个请求的结束时间，加上它的结束机场飞回$0$的时间小于等于总的时间限制，该请求向汇点连边。

但是每次执行完一个请求并未规定一定要飞回$0$机场，也可以飞去其他请求的起点机场，所以两两枚举请求，如果满足时间条件也进行连边。

最后考虑有$k$架飞机，所以再建一个源点，向原来的源点连费用为$0$，流量为$k$的边即可。

最后跑最大费用最大流。

```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
#define time TTTT
using namespace std;
int n,m,k,tim,t[205][205],w[205][205],head[100005];
int vis[100005],dis[100005],cnt=1,cost,ans,time,st,ed;
struct node{
    int a,b,s,t,c;
}q[1005];
struct Edge{
    int v,nx,s,val;
}e[1000005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
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
    for(int i=0;i<=ed;i++) dis[i]=-1e9,vis[i]=0;
    queue<int> q;
    dis[st]=0;
    q.push(st);
    while(!q.empty()){
        int now=q.front();
        q.pop();
        vis[now]=0;
        for(int i=head[now];i;i=e[i].nx){
            int v=e[i].v;
            if(dis[v]<dis[now]+e[i].val&&e[i].s){
                dis[v]=dis[now]+e[i].val;
                if(!vis[v]){
                    vis[v]=1;
                    q.push(v);
                }
            }
        }
    }
    return dis[ed]!=-1e9;
}
int dfs(int now,int ma){
    if(now==ed){
        ans+=ma;
        return ma;
    }
    vis[now]=time;
    int used=0,t;
    for(int i=head[now];i;i=e[i].nx){
        int v=e[i].v;
        if((vis[v]!=time||v==ed)&&e[i].s&&dis[v]==dis[now]+e[i].val){
            if(t=dfs(v,min(ma-used,e[i].s))){
                e[i].s-=t;
                e[i^1].s+=t;
                cost+=t*e[i].val;
                used+=t;
                if(used==ma) break;
            }
        }
    }
    return used;
}
signed main(){
    n=read(),m=read(),k=read(),tim=read();
    for(int i=0;i<n;i++)
        for(int j=0;j<n;j++)
            t[i][j]=read();
    for(int i=0;i<n;i++)
        for(int j=0;j<n;j++)
            w[i][j]=read();
    st=m*2+5,ed=m*2+10;
    for(int i=1;i<=m;i++){
        q[i].a=read();
        q[i].b=read();
        q[i].s=read();
        q[i].t=read();
        q[i].c=read();
    }
    for(int i=1;i<=m;i++){
        add(i*2-1,i*2,q[i].c,1);
        add(i*2,i*2-1,-q[i].c,0);
        if(q[i].t+t[q[i].b][0]<=tim){
            add(i*2,ed,-w[q[i].b][0],1e9);
            add(ed,i*2,w[q[i].b][0],0);
        }
        else continue;
        if(t[0][q[i].a]<=q[i].s){
            add(st+1,i*2-1,-w[0][q[i].a],1e9);
            add(i*2-1,st+1,w[0][q[i].a],0);
        }
        for(int j=1;j<=m;j++){
            if(q[i].t+t[q[i].b][q[j].a]<=q[j].s){
                add(i*2,j*2-1,-w[q[i].b][q[j].a],1e9);
                add(j*2-1,i*2,w[q[i].b][q[j].a],0);
            }
        }
    }
    add(st,st+1,0,k);
    add(st+1,st,0,0);
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