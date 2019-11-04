---
title: 题解 ABC138F 【Coincidence】
date: 2019-11-04 10:40:20
top: 1
tag: 
- 题解
- AtCoder
- 数位dp
---
[传送门](https://atcoder.jp/contests/abc138/tasks/abc138_f)

题意：求$L\leq x\leq y\leq R$且满足$y\% x=y\oplus x$的$(x,y)$的对数。$(1\leq L\leq R\leq 10^{18})$

$y\% x$显然很不好做，我们需要转化一下。

我们发现：

$1.$ 当$2x\leq y$，有$y-x>y\% x$；

$2.$ 当$2x>y$，有$y-x=y\%x$。

$3.$ $y\oplus x\geq y-x$。

于是：

$2x\leq y$时，不存在$y\oplus x=y\% x$。

所以$2x>y$，即$x$和$y$的位数相同，最高位同时为$1$。

那么问题就转化成，求$y-x=y\oplus x$的$(x,y)$的对数。

满足$y-x=y\oplus x$，那么$y$二进制下为$1$，$x$为$0$或$1$，$y$二进制下为$0$，$x$必为$0$。

考虑数位$dp$，这种类型的数位$dp$不像常规的数位$dp$，用$0\sim r$的答案减去$0\sim l-1$的答案。

我们考虑枚举哪一位为最高位，然后$dp[i][x1][x2]$表示前$i$位，数的大小有没有达到下界$L$，有没有达到上界$R$的方案数，转移时先枚举$y$，再枚举$x$。

还有一道[类似的题](https://www.luogu.org/problem/CF1245F)，做法是相同的。

$Code\ Below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define int long long
#define hh puts("")
#define pc putchar
#define mo 1000000007
//#define getchar() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
//char buf[1<<21],*p1=buf,*p2=buf;
using namespace std;
const int N=65;
int l,r,lenl,lenr,pl[N],pr[N],dp[N][2][2],ans;
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
void write(int x){if(x<0){x=-x,pc('-');}if(x>9) write(x/10);pc(x%10+48);}
void writeln(int x){write(x),hh;}
void writesp(int x){write(x),pc(' ');}
int dfs(int pos,int x1,int x2){
    if(!pos) return 1;
    if(dp[pos][x1][x2]!=-1) return dp[pos][x1][x2];
    int &res=dp[pos][x1][x2];res=0;
    int t1=x1?pl[pos]:0,t2=x2?pr[pos]:1;
    for(int y=t1;y<=t2;y++)
        for(int x=t1;x<=y;x++)
            res=(res+dfs(pos-1,x1&(x==t1),x2&(y==t2)))%mo;
    return res;
}
signed main(){
    memset(dp,-1,sizeof(dp));
    l=read(),r=read();
    while(l){
        pl[++lenl]=l&1;
        l>>=1;
    }
    while(r){
        pr[++lenr]=r&1;
        r>>=1;
    }
    for(int i=lenl;i<=lenr;i++) ans=(ans+dfs(i-1,i==lenl,i==lenr))%mo;//枚举哪个最高位为1 
    write(ans);
    return 0;
}
```