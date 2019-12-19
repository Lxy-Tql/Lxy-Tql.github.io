---
title: 题解 luoguP5008 【[yLOI2018] 锦鲤抄】
date: 2019-12-19 13:01:07
top: 1
tag: 
- 题解
- Luogu
- tarjan
- 贪心
---
[传送门](https://www.luogu.com.cn/problem/P5008)

首先考虑有向无环图怎么做。

有一个贪心的想法，按权值从大到小取，那么我们可以发现除了入度为$0$的一定取不到以外，总能安排一种顺序使得我们取到想要取的点。

考虑有环的情况：缩点后整张图仍然是一个有向无环图，那么对于一个有入度的强连通分量，发现也能安排一种顺序取完这个强联通分量内所有的点。对于缩完点之后的根节点代表的强联通分量，可以发现总会剩下一个点一定取不到。

那么思路就很清晰了，先缩点，然后拿个堆从大到小取点，如果一个点所在强联通分量没有入度，那么判一下这个强连通分量内是不是只剩它这个点没有取，如果是，则不能取这个点。

$Code\ Below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
#define pc putchar
#define getchar() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
char buf[1<<21],*p1=buf,*p2=buf;
using namespace std;
const int N=500005,M=1000005;
int n,m,k,head[N],cnt,col[N],dfn[N],low[N],dep,st[N],vis[N];
int top,col_cnt,sz[N],ans,in[N];
struct Edge{
    int u,v,nx;
}e[M<<1];
struct node{
    int v,id;
    friend bool operator < (node A,node B){
        return A.v<B.v;
    }
};
priority_queue<node> q;
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
void write(int x){if(x<0){x=-x,pc('-');}if(x>9) write(x/10);pc(x%10+48);}
void writeln(int x){write(x),hh;}
void writesp(int x){write(x),pc(' ');}
void add(int x,int y){
    e[++cnt].u=x;
    e[cnt].v=y;
    e[cnt].nx=head[x];
    head[x]=cnt;
}
void tarjan(int now){
    dfn[now]=low[now]=++dep;
    st[++top]=now;vis[now]=1;
    for(int i=head[now];i;i=e[i].nx){
        int v=e[i].v;
        if(!dfn[v]){
            tarjan(v);
            low[now]=min(low[now],low[v]);
        }
        else if(vis[v]) low[now]=min(low[now],dfn[v]);
    }
    if(dfn[now]==low[now]){
        int t;
        col_cnt++;
        do{
            t=st[top--];
            vis[t]=0;
            col[t]=col_cnt;
            sz[col_cnt]++;
        }while(t!=now);
    }
}
signed main(){
    n=read(),m=read(),k=read();
    for(int i=1;i<=n;i++) q.push((node){read(),i});
    for(int i=1;i<=m;i++){
        int x=read(),y=read();
        add(x,y);
    }
    for(int i=1;i<=n;i++)
        if(!dfn[i])
            tarjan(i);
    for(int i=1;i<=cnt;i++)
        if(col[e[i].u]!=col[e[i].v])
            in[col[e[i].v]]++;
    for(int i=1;i<=k;i++){
        while(!q.empty()){
            node now=q.top();
            q.pop();
            int C=col[now.id];
            if(in[C]){//所在强连通分量有入度
                ans+=now.v;
                break;
            }
            else{
                if(sz[C]!=1){//所在强连通分量如果剩一个点就取不了
                    ans+=now.v;
                    sz[C]--;
                    break;
                }
            }
        }
    }
    write(ans);
    return 0;
}
```
