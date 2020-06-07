---
title: 题解 luoguP3401 【洛谷树】
date: 2020-06-07 18:41:04
top: 1
tag: 
- 题解
- Luogu
- LCA
- 树链剖分
- 位运算
- 线段树
---
[传送门](https://www.luogu.org/problemnew/show/P3401)

半年没写树剖了，就当模板练一下

首先设根节点到$i$路径上的异或和为$val[i]$，则$i$到$j$路径的异或和为$val[i]\oplus val[j]$。

对于$i$到$j$所有子路径的异或和的和，对每一位分开来考虑，对于第$i$位，发现贡献就是$(1<<i)\times gs(0)\times gs(1)$，其中$gs(x)$为$i$到$j$路径上$x$的个数。

$gs(0/1)$显然可以树剖$+$线段树维护，对于一次修改，直接翻转子树内的$0,1$个数即可，用树剖$+$线段树可以轻松维护。

$Code\ Below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define int long long
#define hh puts("")
#define pc putchar
#define ls(x) ((x)<<1)
#define rs(x) ((x)<<1|1)
//#define getchar() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
//char buf[1<<21],*p1=buf,*p2=buf;
using namespace std;
const int N=30005;
int n,q,cnt,head[N],val[N],f[N],eg[N],sz[N],d[N],son[N];
int top[N],id[N],rk[N],sum[10][N<<2],tag[10][N<<2];
struct Edge{
    int v,nx,s;
}e[N<<1];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
void write(int x){if(x<0){x=-x,pc('-');}if(x>9) write(x/10);pc(x%10+48);}
void writeln(int x){write(x),hh;}
void writesp(int x){write(x),pc(' ');}
void add(int x,int y,int s){
    e[++cnt].v=y;
    e[cnt].nx=head[x];
    e[cnt].s=s;
    head[x]=cnt;
}
void dfs1(int now,int fa){
    sz[now]=1;d[now]=d[fa]+1;
    for(int i=head[now];i;i=e[i].nx){
        int v=e[i].v;
        if(v==fa) continue;
        val[v]=val[now]^e[i].s;
        dfs1(v,now);
        f[v]=now;
        eg[v]=e[i].s;
        sz[now]+=sz[v];
        if(sz[v]>sz[son[now]]) son[now]=v;
    } 
}
void dfs2(int now,int fa){
    top[now]=fa;
    id[now]=++cnt;
    rk[cnt]=now;
    if(son[now]) dfs2(son[now],fa);
    for(int i=head[now];i;i=e[i].nx){
        int v=e[i].v;
        if(f[now]==v||v==son[now]) continue;
        dfs2(v,v);
    }
}
void build(int l,int r,int k){
    if(l==r){
        for(int i=0;i<=9;i++) sum[i][k]=(val[rk[l]]>>i)&1;
        return;
    }
    int mid=(l+r)>>1;
    build(l,mid,ls(k));
    build(mid+1,r,rs(k));
    for(int i=0;i<=9;i++) sum[i][k]=sum[i][ls(k)]+sum[i][rs(k)];
}
void push_down(int tt,int l,int r,int k){
    if(tag[tt][k]){
        int mid=(l+r)>>1;
        tag[tt][ls(k)]^=1;
        tag[tt][rs(k)]^=1;
        sum[tt][ls(k)]=mid-l+1-sum[tt][ls(k)];
        sum[tt][rs(k)]=r-mid-sum[tt][rs(k)];
        tag[tt][k]=0;
    }
}
void update(int l,int r,int x,int y,int tt,int k){
    if(x<=l&&r<=y){
        sum[tt][k]=r-l+1-sum[tt][k];
        tag[tt][k]^=1;
        return;
    }
    push_down(tt,l,r,k);
    int mid=(l+r)>>1;
    if(x<=mid) update(l,mid,x,y,tt,ls(k));
    if(mid+1<=y) update(mid+1,r,x,y,tt,rs(k));
    sum[tt][k]=sum[tt][ls(k)]+sum[tt][rs(k)];
}
int query(int l,int r,int x,int y,int tt,int k){
    if(x<=l&&r<=y) return sum[tt][k];
    push_down(tt,l,r,k);
    int mid=(l+r)>>1,res=0;
    if(x<=mid) res+=query(l,mid,x,y,tt,ls(k));
    if(mid+1<=y) res+=query(mid+1,r,x,y,tt,rs(k));
    return res;
}
int LCA(int x,int y){
    while(top[x]!=top[y]){
        if(d[top[x]]>d[top[y]]) x=f[top[x]];
        else y=f[top[y]]; 
    }
    if(d[x]>d[y]) return y;
    return x;
}
int ask(int x,int y,int tt){
    if(d[x]<d[y]) swap(x,y);
    int lca=LCA(x,y),tot=0,gs=0;
    tot=d[x]+d[y]-2*d[lca]+1;
    while(top[x]!=top[y]){
        if(d[top[x]]<d[top[y]]) swap(x,y);
        gs+=query(1,n,id[top[x]],id[x],tt,1);
        x=f[top[x]];
    }
    if(id[x]>id[y]) swap(x,y);
    gs+=query(1,n,id[x],id[y],tt,1);
    return gs*(tot-gs);
}
signed main(){
    n=read(),q=read();
    for(int i=1;i<n;i++){
        int x=read(),y=read(),s=read();
        add(x,y,s),add(y,x,s);
    }
    dfs1(1,0);cnt=0;
    dfs2(1,1);
    build(1,n,1);
    for(int i=1;i<=q;i++){
        int opt=read();
        if(opt==1){
            int x=read(),y=read();
            int ans=0;
            for(int i=0;i<=9;i++) ans+=ask(x,y,i)*(1<<i);
            writeln(ans);
        }
        if(opt==2){
            int x=read(),y=read(),s=read();
            if(d[x]<d[y]) swap(x,y);
            for(int i=0;i<=9;i++)
                if(((eg[x]>>i)&1)^((s>>i)&1))
                    update(1,n,id[x],id[x]+sz[x]-1,i,1);
            eg[x]=s;
        }
    }
    return 0;
}
```
