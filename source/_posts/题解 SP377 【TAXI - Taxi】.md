---
title: 题解 SP377 【TAXI - Taxi】
date: 2019-06-02 14:58:55
top: 1
tag: 
- 题解
- spoj
- 二分图
- 匈牙利
---
[传送门](https://www.luogu.org/problemnew/show/SP377)

每辆出租车只能接1人，显然是二分图匹配。

二分图左部为车，右部为人，车根据距离向能接到的人连边。

跑匈牙利即可。

```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
#define time TTTT
using namespace std;
int n,m,s,lim,head[1005],vis[305],match[505];
int cnt,time,ans;
struct Edge{
    int v,nx;
}e[100005];
struct node{
    int x,y;
}peo[10005],tx[10005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
inline void add(int x,int y){
    e[++cnt].v=y;
    e[cnt].nx=head[x];
    head[x]=cnt;
}
inline int dis(int x,int y){
    return abs(peo[x].x-tx[y].x)+abs(peo[x].y-tx[y].y);
}
bool find(int x){
    for(int i=head[x];i;i=e[i].nx){
        int v=e[i].v;
        if(vis[v]==time) continue;
        vis[v]=time;
        if(!match[v]||find(match[v])){
            match[v]=x;
            return 1;
        }
    }
    return 0;
}
signed main(){
    int T=read();
    while(T--){
        memset(head,0,sizeof(head));
        memset(vis,0,sizeof(vis));
        memset(match,0,sizeof(match));
        cnt=0,time=0,ans=0;
        n=read(),m=read();
        s=read(),lim=read();
        lim=s*lim;
        for(int i=1;i<=n;i++){
            peo[i].x=read();
            peo[i].y=read();
        }
        for(int i=1;i<=m;i++){
            tx[i].x=read();
            tx[i].y=read();
        }
        for(int i=1;i<=n;i++)
            for(int j=1;j<=m;j++)
                if(dis(i,j)*200<=lim)
                    add(i,j);
        for(int i=1;i<=n;i++){
            time++;
            ans+=find(i);
        }
        printf("%d\n",ans);
    }
    return 0;
}
```