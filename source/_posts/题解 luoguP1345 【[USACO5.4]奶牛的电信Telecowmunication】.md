---
title: 题解 luoguP1345 【[USACO5.4]奶牛的电信Telecowmunication】
date: 2019-03-27 20:51:28
top: 1
tag: 
- 题解
- Luogu
- 网络流
- 最小割
---
[传送门](https://www.luogu.org/problemnew/show/P1345)

众所周知，网络流可以求最小割，但割的是割边。本题一眼看就可以知道，题意要求割掉最少的点使起点到终点不连通。

最小割怎么处理点呢？我们想，只要把点取不取转化到边权就好办了。

考虑拆点，把一个点$i$拆成$i$和$i+n$。$i$向$i+n$连一条边权为$1$的边，再把连向这个点的边都连到$i$上，这个点连向其他点的边都连到$i+n$上。那么只要割掉这条边权为$1$的边，就等价于把这个点给割掉了。建完图后就是求最小割的模板了。注意起点是$st+n$而不是$st$。

总的来说，网络流几乎没有裸题，都需要动脑子(~~看题解~~)去建图。拆点就是一种比较常见的方法。

代码：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define lowbit(x) (x)&(-x)
#define oo (1e18)
#define ll long long
#define LL unsigned long long
#define hh puts("")
using namespace std;
int n,m,cnt=1,tot,ans,st,ed,head[10005],cur[10005],d[10005];
struct Edge{
    int v,nx,s;
}e[2000005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline void add(int x,int y,int z){
    e[++cnt].v=y;
    e[cnt].s=z;
    e[cnt].nx=head[x];
    head[x]=cnt;
}
inline bool bfs(){
    for(int i=0;i<=10000;i++) d[i]=0,cur[i]=head[i];
    d[st]=1;
    queue<int> q;
    q.push(st);
    while(!q.empty()){
        int now=q.front();
        q.pop();
        for(int i=head[now];i;i=e[i].nx){
            int v=e[i].v;
            if(e[i].s&&!d[v]){
                d[v]=d[now]+1;
                if(v==ed) return 1;
                q.push(v);
            }
        }
    }
    return 0;
}
int dfs(int now,int ma){
    if(now==ed){
        ans+=ma;
        return ma;
    }
    int used=0,t;
    for(int i=cur[now];i;i=e[i].nx){
        cur[now]=i;
        int v=e[i].v;
        if(e[i].s&&d[v]==d[now]+1){
            if(t!=dfs(v,min(e[i].s,ma-used))){
                used+=t;
                e[i].s-=t;
                e[i^1].s+=t;
                if(used==ma) break;
            }
        }
    }
    return used;
}
signed main(){
    n=read(),m=read();
    st=read()+n,ed=read();
    for(int i=1;i<=n;i++){
        add(i,i+n,1);
        add(i+n,i,0);
    }
    for(int i=1;i<=m;i++){
        int x=read(),y=read();
        add(x+n,y,1e9);
        add(y,x+n,0);
        add(y+n,x,1e9);
        add(x,y+n,0);
    }
    while(bfs()) dfs(st,1e9);
    printf("%d",ans);
    return 0;
}
```