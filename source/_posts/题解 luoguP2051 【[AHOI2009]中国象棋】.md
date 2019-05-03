---
title: 题解 luoguP2051 【[AHOI2009]中国象棋】
date: 2019-04-15 19:18:37
top: 1
tag:
- 题解
- Luogu
- 动态规划
---
[传送门](https://www.luogu.org/problemnew/show/P2051)

经过多次爆蛋，还是~~秒~~切了此题。

一道少数没看题解做的题，~~哇居然是紫题我好牛逼~~。

开始~~不~~正经的讲题目：很显然发现这是一道动态规划题，先考虑设计状态。

很容易想到的状态是$dp[i][j]$表示前$i$行放上$j$个炮的方案数。但转移时我们就发现，这个状态所包含的信息太少了，从第$i-1$行转移到第$i$行，好像根本无法判断第$i$行怎么放，辣怎么办哩？

观察到炮的性质：一行或者一列最多只能放两个，放三个就会互相搞~~基~~起来。我们想要以行为层次进行转移，那么很显然我们要保存列的状态。

因为一列最多放一个或者两个，辣么我们用$dp[i][j][k]$表示前$i$行，有$j$列只放了一个，有$k$列放了两个的方案数，这个状态就十分的完美啦，完结撒花~

停停停，这都写了还要讲怎么转移吗？随便$yy$一下就好了啊？

显然根据我们设计的状态，是以$i$为层次进行转移的，辣么如何从第$i-1$行转移到第$i$行？我们欢快的进行分类讨论，发现情况并不多：

#### 1.第$i$行什么都不放。太愚蠢了。
$$
f[i][j][k]=f[i][j][k]+f[i-1][j][k]
$$
#### 2.放1个在空的列。根据状态很容易发现空的列是$m-j-k$。那么方案显然了：
$$
f[i][j][k]=f[i][j][k]+f[i-1][j-1][k]\times(m-(j-1)-k)
$$
#### 3.放1个在有1个的列。
$$
f[i][j][k]=f[i][j][k]+f[i-1][j+1][k-1]\times(j+1)
$$
#### 4.放2个都在空的列。这里就用到排列了，是之前状态乘以$C($之前空的列，$2)$。
$$
f[i][j][k]=f[i][j][k]+f[i-1][j-2][k]\times C(m-(j-2)-k)
$$
#### 5.放1个在空的列,1个在有1个的列。同样乘法原理：
$$
f[i][j][k]=f[i][j][k]+f[i-1][j][k-1]\times j\times (m-j-(k-1))
$$
#### 6.放2个在有1个的列。我一开始愚蠢到忘记考虑这种了。
$$
f[i][j][k]=f[i][j][k]+f[i-1][j+2][k-2]\times C(j+2)
$$
初始化很简单：$f[0][0][0]=1$。

完结撒花~，中途记得取模。

还需要代码吗？我想是的：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define oo (1e18)
#define int long long
#define hh puts("")
#define mo 9999973
using namespace std;
int n,m,f[105][105][105];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline int C(int aa){//C(aa,2)
    return (aa*(aa-1)/2)%mo;
}
signed main(){
    n=read(),m=read();
    f[0][0][0]=1;
    for(int i=1;i<=n;i++)
        for(int j=0;j<=m;j++)
            for(int k=0;j+k<=m&&j+k<=2*i;k++){
                f[i][j][k]=(f[i][j][k]+f[i-1][j][k])%mo;//什么都不放 
                if(j>=1) f[i][j][k]=(f[i][j][k]+f[i-1][j-1][k]*(m-(j-1)-k)%mo)%mo;//放1个在空的列 
                if(k>=1) f[i][j][k]=(f[i][j][k]+f[i-1][j+1][k-1]*(j+1)%mo)%mo;//放1个在有1个的列 
                if(j>=2) f[i][j][k]=(f[i][j][k]+f[i-1][j-2][k]*C(m-(j-2)-k)%mo)%mo;//放2个都在空的列 
                if(k>=1) f[i][j][k]=(f[i][j][k]+f[i-1][j][k-1]*j%mo*(m-j-(k-1))%mo)%mo;//放1个在空的列,1个在有1个的列 
                if(k>=2) f[i][j][k]=(f[i][j][k]+f[i-1][j+2][k-2]*C(j+2)%mo)%mo;//放2个在有1个的列 
            }
    int ans=0;
    for(int i=0;i<=m;i++)
        for(int j=0;i+j<=m&&(i+j)<=2*n;j++)
            ans=(ans+f[n][i][j])%mo;
    printf("%lld",ans);
    return 0;
}
```