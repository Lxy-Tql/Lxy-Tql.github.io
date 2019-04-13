---
title: 题解 luoguP1772 【[ZJOI2006]物流运输】
date: 2019-03-07 21:15:16
top: 1
tag:
- 题解
- Luogu
- 动态规划
---
[传送门](https://www.luogu.org/problemnew/show/P1772)

模拟赛居然考了这道题，前一天刚看过，结果看了舍不得(~~不会~~)做，结果只骗到30pt

讲课人：很容易想到最短路+$dp$(~~我靠一点都不容易~~)

模拟赛后分析，才知道是处理出第i天到第j天都走同一条最短路的花费为$co[i][j]$

然后进行$dp$，$dp[i]$表示前i天的最小花费

转移方程很好想:$dp[i]$ $=$ $min$ $($ $dp[j]$ $+$ $co[j+1][i]$ $\times$ $(i-j)$ $+$ $k$ $)$，预处理要赋值为$co[1][i]$ $\times$ $i$

$dp$方程的意思，即在第$j+1$天改变路线，第$j+1$天~第$i$天都走同一条路线

那么如何处理$co[i][j]$?

很简单，对于每一个$(i,j)$，先把$i$到$j$天之间封闭的码头全部设为不可走，跑一遍最短路即可，初值为无穷

数据辣么小，跑几遍以及跑什么都没关系~~嘤嘤嘤~~

那我们就十分愉♂快的解决了此题~~~

愉♂快的提交了然后居然只有90pt

~~原谅我无耻的打开题解~~

啊啊啊原来要开$long$ $long$(明明数据辣么小)

献上代码：
```cpp
#include<bits/stdc++.h>
#define soo (1e8)
#define ll long long
using namespace std;
int d,cnt,head[25],dis[25],vis[25],cant_vis[25];
ll co[105][105],dp[105];
int n,m,k,ee,cl[25][105];
struct Edge{
    int v,nx,s;
}e[10005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+ch-'0';ch=getchar();}
    return ret*ff;
}
void add(int x,int y,int z){
    e[++cnt].v=y;
    e[cnt].s=z;
    e[cnt].nx=head[x];
    head[x]=cnt;
}
void spfa(){//爱跑什么跑什么
    for(int i=1;i<=m;i++) dis[i]=soo,vis[i]=0;
    queue<int> q;
    dis[1]=0;
    q.push(1);
    while(!q.empty()){
        int x=q.front();
        q.pop();
        vis[x]=0;
        for(int i=head[x];i;i=e[i].nx){
            int v=e[i].v;
            if(cant_vis[v]) continue;
            if(dis[v]>dis[x]+e[i].s){
                dis[v]=dis[x]+e[i].s;
                if(!vis[v]){
                    vis[v]=1;
                    q.push(v);
                }
            }
        }
    }
}
signed main(){
    n=read(),m=read(),k=read(),ee=read();
    for(int i=1;i<=ee;i++){
        int x=read(),y=read(),z=read();
        add(x,y,z);
        add(y,x,z);
    }
    d=read();
    for(int i=1;i<=d;i++){
        int t=read(),x=read(),y=read();
        for(int j=x;j<=y;j++) cl[t][j]=1;
    }
    //cl[i][j]表示第i个码头在第j天不能走
    for(int i=1;i<=n;i++)
        for(int j=1;j<=n;j++){
            memset(cant_vis,0,sizeof(cant_vis));
            for(int r=i;r<=j;r++)
                for(int l=1;l<=m;l++)
                    if(cl[l][r]) cant_vis[l]=1;
            spfa();
            co[i][j]=dis[m];
        }
    memset(dp,0x7f,sizeof(dp));
    for(int i=1;i<=n;i++){
        dp[i]=(ll)co[1][i]*i;
        for(int j=i-1;j>=0;j--)
            dp[i]=min(dp[i],dp[j]+co[j+1][i]*(i-j)+k);
    }
    printf("%lld",dp[n]);
    return 0;
}
```