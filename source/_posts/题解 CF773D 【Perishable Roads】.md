---
title: 题解 CF773D 【Perishable Roads】
date: 2019-03-07 21:11:59
top: 1
tag: 
- 题解
- Codeforces
- 最短路
- 贪心
---
[传送门](https://www.luogu.org/problemnew/show/CF773D)

简述一下题意：给出$n$个点的完全图，对于完全图中的每个点$i$，$i$作为终点时，要使其他每个点到点$i$的“距离”和最小，对于每个点都输出这个最小值。
这里的“距离”是指对于其他每个点，那个点到点$i$路径上的最小值。且对于每个点$i$，计算答案时应保证图内每条边的方向一定。（有点难解释，可以参考原文）

**题意很难表述清楚，建议看懂原题后再来看此题解。**

考虑对于每个终点$i$，最后连接所有点后图的形态，应该是一棵树接上一条链。如图：![](https://s2.ax1x.com/2019/04/30/E8iUmR.jpg)

------------
### 我会贪心&&搜索！
从终点开始倒搜，对于当前搜到的点$now$，每次找到未$vis$的且与$now$相连的边权最小的点，向那个点继续搜。

~~很显然这个想法是非常非常错误的，直接排除~~

------------
### 考虑优化贪心：
既然让每个点到终点路径上的最小边权和最小，那么很容易想到将所有点都连到边权最小的边的一端，再从这个点连向终点。$($下文我们将这个点称为“最小点”$)$

但这个还是错误的，如果连向终点的那条边权值特别大$(INF)$，那么答案就会非常劣。如图：![](https://s2.ax1x.com/2019/04/30/E8itX9.jpg)

那怎么办呢？我们就考虑让“最小点”去间接的连向终点，即从那些直接连向“最小点”的点中取一些出来，与“最小点”构成一条链，使这一条链加上那棵树的答案更优。我们称这条链$($起点为最小点,终点为$t$ $)$的答案为$dis[t]$。如图：![](https://s2.ax1x.com/2019/04/30/E8ia01.jpg)

怎么计算答案呢？我们设那条链上除终点外有$x$个点，那么那棵树上就有$n-1-x$个点，设最小边长度为$minn$，那么答案为$dis[t]$+$minn$ $\times$ $(n-1-x)$。

这个$x$很难计算，考虑消去。即计算$dis[t]$前先对所有边权减去一个$minn$，设新链答案为$dis'[t]$，那么答案会变成$($ $dis'[t]$+$minn$ $\times$ $x$ $)$+$minn$ $\times$ $(n-1-x)$，即$dis'[t]$+$minn$ $\times$ $(n-1)$，$x$就消去了。所以我们计算$dis'[t]$即可。

因为要求最优解，我们跑最短路求$dis$ $($定义见上$)$。一开始的状态是上面图2，即向终点直接连边，所以赋为终点与最小点的边权$($详见代码$)$。还有一种状态，即考虑那条链上有3个点。设加入的为点$j$，那么链的答案可能为最小点到终点的答案加上$j$到终点的答案，即$f[i][j]*2$ $($ $f$数组为邻接矩阵，$i$是终点 $)$。最后$dij$松弛即可$($模板$)$。


```cpp
#include<bits/stdc++.h>
using namespace std;
int n,f[3005][3005],vis[3005],dis[3005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline void write(int x){
    if(x<0){putchar('-');x=-x;}
    if(x>9)write(x/10);
    putchar(x%10+48);
}
void dij(int st){
    for(int i=1;i<=n;i++){
        dis[i]=f[st][i];
        for(int j=1;j<=n;j++)
            if(i!=j) dis[i]=min(dis[i],f[i][j]*2);
    }
    vis[st]=1;
    for(int i=1;i<=n-1;i++){
        int minn=2e9+1,k;
        for(int j=1;j<=n;j++)
            if(!vis[j]&&dis[j]<minn){
                minn=dis[j];
                k=j;
            }
        vis[k]=1;
        for(int j=1;j<=n;j++)
            if(!vis[j])
                dis[j]=min(dis[j],dis[k]+f[j][k]);
    }
}
int main(){
    int minn=2e9+1,k;
    n=read();
    for(int i=1;i<=n-1;i++)
        for(int j=1;j<=n-i;j++){
            f[i][i+j]=f[i+j][i]=read();
            if(f[i][i+j]<minn){
                minn=f[i][i+j];
                k=i;
            }
        }
    for(int i=1;i<=n-1;i++)
        for(int j=1;j<=n-i;j++){
            f[i][i+j]-=minn;
            f[i+j][i]=f[i][i+j];
        }        
    dij(k);
    for(int i=1;i<=n;i++) printf("%d ",dis[i]+minn*(n-1));
    return 0;
}
```