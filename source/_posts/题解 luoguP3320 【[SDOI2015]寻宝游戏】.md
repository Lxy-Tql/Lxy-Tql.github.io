---
title: 题解 luoguP3320 【[SDOI2015]寻宝游戏】
date: 2019-03-14 08:30:24
top: 1
tag: 
- 题解
- Luogu
- LCA
- set
---
[传送门](https://www.luogu.org/problemnew/show/P3320)

看到建虚树的做法？？？~~不会~~

这题要求每一次加减操作都计算答案，那么我们考虑加入一个点的贡献。

慢慢分析：首先一个点或者没有点，肯定没有贡献，为0。

然后是两个点，随便从一个点开始，走到另一个点再返回，贡献为2 $\times$ $dis(x,y)$。

回忆一下树上两点距离：$x$到$y$的距离$=$ $x$到根的距离$+$ $y$到根的距离$-$ $lca(x,y)$到根的距离$\times$ 2。($lca$想怎么求怎么求)

再增加一个点呢？树上有3个点，怎么走能使距离最短？应该是按遍历时的时间戳走咯。假设时间戳为1，2，3，那么贡献就是1->2 $+$ 2->3 $+$ 3->1。(箭头表示两点之间距离)

那么不管几个点，我们发现按时间戳走肯定最快。所以考虑维护加入点的时间戳。蒟蒻我不会$splay$，只能甩出一手$STL$大法$set$。(~~STL大法好~~)

每插入一个元素，考虑增加的贡献，是先删去它时间戳($set$里面)左边和右边的贡献，再加上它和左边以及它和右边的贡献。删去一个元素同理。这题做完，对$set$的使用以后就会很熟练了。

注意特判一个点的情况以及开$long\ long$。

上代码：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define oo (1e18)
#define int long long
#define LL unsigned long long
#define ite set<int>::iterator
#define ls(x) (x<<1)
#define rs(x) (x<<1|1)
#define hh puts("")
using namespace std;
int n,m,cnt,head[100005],dfn[100005],bz[100005][20],dis[100005],vis[100005],dep[100005],ans;
set<int> s;
struct Edge{
    int v,nx,s;
}e[200005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline void add(int x,int y,int z){
    e[++cnt].v=y;
    e[cnt].nx=head[x];
    e[cnt].s=z;
    head[x]=cnt;
}
void dfs(int now,int fa){
    dfn[now]=++cnt;//时间戳 
    vis[cnt]=now;//dfs序 
    dep[now]=dep[fa]+1;
    for(int i=head[now];i;i=e[i].nx){
        int v=e[i].v;
        if(v==fa) continue;
        dis[v]=dis[now]+e[i].s;
        bz[v][0]=now;
        dfs(v,now);
    }
}
inline ite lef(ite it){
    if(it==s.begin()) return --s.end();
    return --it;
}
inline ite rig(ite it){
    if(it==--s.end()) return s.begin();
    return ++it;
}
inline int LCA(int x,int y){
    if(dep[x]<dep[y]) swap(x,y);
    for(int i=17;i>=0;i--){
        if(dep[bz[x][i]]>=dep[y])
            x=bz[x][i];
    }
    if(x==y) return x;
    for(int i=17;i>=0;i--)
        if(bz[x][i]!=bz[y][i])
            x=bz[x][i],y=bz[y][i];
    return bz[x][0];
}
signed main(){
    n=read(),m=read();
    for(int i=1;i<n;i++){
        int x=read(),y=read(),z=read();
        add(x,y,z);
        add(y,x,z);
    }
    cnt=0;
    dis[1]=0;
    dfs(1,0);
    for(int k=1;k<=17;k++)
        for(int i=1;i<=n;i++)
            bz[i][k]=bz[bz[i][k-1]][k-1];
    
    for(int i=1;i<=m;i++){
        int x=read();
        if(!s.count(dfn[x])){
            if(s.size()){
                ite it=s.lower_bound(dfn[x]);
                if(it==s.end()) it=s.begin();
                ite L=lef(it);
                int lca1=LCA(vis[*it],vis[*L]);
                int lca2=LCA(vis[*it],x);
                int lca3=LCA(vis[*L],x);
                ans-=dis[vis[*it]]+dis[vis[*L]]-2*dis[lca1];
                ans+=dis[vis[*it]]+dis[x]-2*dis[lca2];
                ans+=dis[vis[*L]]+dis[x]-2*dis[lca3];
            }
            s.insert(dfn[x]);
            printf("%lld\n",ans);
            continue;
        }
        else{
            ite it=s.find(dfn[x]);
            ite L=lef(it),R=rig(it);
            int lca1=LCA(x,vis[*L]);
            int lca2=LCA(x,vis[*R]);
            int lca3=LCA(vis[*L],vis[*R]);
            ans-=dis[x]+dis[vis[*L]]-2*dis[lca1];
            ans-=dis[x]+dis[vis[*R]]-2*dis[lca2];
            ans+=dis[vis[*L]]+dis[vis[*R]]-2*dis[lca3];
            s.erase(dfn[x]);
            printf("%lld\n",ans);
            continue;
        }
    }
    return 0;
}
```
