---
title: 题解 luoguP2857 【[USACO06FEB]稳定奶牛分配Steady Cow Assignment】
date: 2019-07-17 14:09:25
top: 1
tag: 
- 题解
- Luogu
- 网络流
- 最大流
---
[传送门](https://www.luogu.org/problemnew/show/P2857)

考虑$B$的范围比较小，那就$B^2$暴力枚举所有的座次区间。枚举到一个座次从$l$到$r$的区间，意义是奶牛只能选它心目中座次从$l$到$r$区间的牛棚。

然后就是判断区间是否可行，考虑网络流，源点向奶牛连边，奶牛向可选牛棚连边，牛棚向汇点连它容量的边。$Dinic$判断最大流是否是$n$。

不用当前弧优化好像会$T$？

$Code\ Below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
using namespace std;
int n,b,g[1005][25],ans=1e9,st,ed,tot,can[25],head[1025],cnt,d[1025],cur[1025];
struct Edge{
    int v,nx,s;
}e[100005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+ch-'0';ch=getchar();}
    return ret*ff;
}
void write(int x){
    if(x>9) write(x/10);
    putchar(x%10+48);
}
inline void add(int x,int y,int s){
    e[++cnt].v=y;
    e[cnt].s=s;
    e[cnt].nx=head[x];
    head[x]=cnt;
}
inline bool bfs(){
    for(int i=st;i<=ed;i++) cur[i]=head[i],d[i]=0;
    d[st]=1;
    queue<int> q;
    q.push(st);
    while(!q.empty()){
        int now=q.front();
        q.pop();
        for(int i=head[now];i;i=e[i].nx){
            int v=e[i].v;
            if(!d[v]&&e[i].s){
                d[v]=d[now]+1;
                q.push(v);
            }
        }
    }
    return d[ed];
}
int dfs(int now,int ma){
    if(now==ed){
        tot+=ma;
        return ma;
    }
    int used=0,t;
    for(int i=cur[now];i;i=e[i].nx){
        cur[now]=i;
        int v=e[i].v;
        if(d[v]==d[now]+1&&e[i].s){
            if(t=dfs(v,min(e[i].s,ma-used))){
                e[i].s-=t;
                e[i^1].s+=t;
                used+=t;
                if(used==ma) break;
            }
        }
    }
    return used;
}
void build(int l,int r){
    cnt=1;
    memset(head,0,sizeof(head));
    for(int i=1;i<=n;i++) add(st,i,1),add(i,st,0);
    for(int i=1;i<=n;i++)
        for(int j=l;j<=r;j++)
            add(i,n+g[i][j],1),add(n+g[i][j],i,0);
    for(int i=1;i<=b;i++) add(n+i,ed,can[i]),add(ed,n+i,0);
}
signed main(){
    n=read(),b=read();
    st=0,ed=n+b+1;
    for(int i=1;i<=n;i++)
        for(int j=1;j<=b;j++)
            g[i][j]=read();
    for(int i=1;i<=b;i++) can[i]=read();
    for(int i=1;i<=b;i++)
        for(int j=i;j<=b;j++){
            if(j-i+1>=ans) continue;
            build(i,j);
            tot=0;
            while(bfs()) dfs(st,1e9);
            if(tot==n) ans=j-i+1;
        }
    printf("%d",ans);
    return 0;
}
```