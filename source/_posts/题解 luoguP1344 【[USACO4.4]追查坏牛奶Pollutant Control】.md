---
title: 题解 luoguP1344 【[USACO4.4]追查坏牛奶Pollutant Control】
date: 2019-03-27 20:49:56
top: 1
tag: 
- 题解
- Luogu
- 网络流
- 最小割
---
[传送门](https://www.luogu.org/problemnew/show/P1344)

这是一道很好的套路题。助你掌握新套路。

第一问太愚蠢了，求最小割，学过网络流应该都会。

重点是第二问，求最小割割的最少边数。太难了，默默打开题解，$get$到新套路。

建图时，我们将边的权值乘以$(($一个较大的数$a)+1)$，设新的图的最小割为$newans$，那么原图最小割为$newans/a$，最小割割的最少边数为$newans\%a$。

原理(摘自题解)：因为最小割的边集$E=\{w_1,w_2...w_n\}$中有
$$
w_1+w_2+w_3…+w_n=ans
$$
其中$ans$为本来的最小割。

所以必然有：
$$
w_1\times a+w_2\times a+w_3\times a…+w_n\times a=ans\times a
$$
于是又有：
$$
w1\times a+1+w2\times a+1+w3\times a+1…+wn\times a+1=ans\times a+k
$$
$k$为最小割的边数，$k<=m<=1000$

最后得到结论：原图最小割为$newans/a$，最小割割的最少边数为$newans\%a$。

于是这题就解决了：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define lowbit(x) (x)&(-x)
#define oo (1e18)
#define int long long
#define LL unsigned long long
#define hh puts("")
using namespace std;
int n,m,cnt=1,tot,ans,st,ed,head[10005],cur[10005],d[10005],base=2003;
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
    for(int i=0;i<=ed;i++) d[i]=0,cur[i]=head[i];
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
            if(t=dfs(v,min(e[i].s,ma-used))){
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
    st=1,ed=n;
    for(int i=1;i<=m;i++){
        int x=read(),y=read(),z=read();
        add(x,y,z*base+1),add(y,x,0);
    }
    while(bfs()) dfs(st,1e16);
    printf("%lld %lld",ans/base,ans%base);
    return 0;
}
```
拓展一下，也就是原题$($这题简化过$)$：我们求出了边的数量，但如果要求这些边的编号，又应该怎么做？

参考了[这篇博客](https://blog.csdn.net/zhangjianjunab/article/details/79983408)：

首先，网络流的核心是从起点通过多条路径到终点，那么，我们可以想出，这每一条合格$($可以从起点流向终点$)$的路径，一定会有其中一条或多条边满流了，那么就代表这条边是这条路径的核心，去掉这条边，整张图的完整流量就会减去这条边的流量，我们称这些边为贡献边。

那么，在最小割所求的最小容量中所割掉的边的集合中，一定全部是贡献边。显然。

那么，怎么求这些边呢$($编号按字典序排并且要最少的边$)$？

由于这些贡献边控制了流量，所以，我们把它删除掉，再流一遍，会得到一个新的$ans(ans$可能是0$)$，$ans+$这条边的权值$=$不删这条边的最大流，依靠这条等式，就可以去判断了，最后排序即可。