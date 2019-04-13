---
title: 题解 luoguP2155 【[SDOI2008]沙拉公主的困惑】
date: 2019-04-10 07:09:13
top: 1
tag:
- 题解
- Luogu
- 欧拉函数
- 逆元
- 数论
---
[传送门](https://www.luogu.org/problemnew/show/P2155)

这题真喵喵的毒瘤，写的时候被时空双卡。

~~为了发泄愤怒，我来水一篇题解~~

题意：求 $1-n!$ 中与 $m!$ 互质的数的个数 $(m<=n)$

我们发现小于 $m!$ 的数中，与 $m!$ 互质的数有$φ(m!)$个，那如何求大于 $m!$ 的数中与 $m!$ 互质的数的个数呢？

引理：若$a,b$互质，则$a+b\times k$与$b$互质。

证明：当$gcd(a,b)=1$时，$gcd(b,a\% b)=1$

$\because a\% b=a-b\times k$

$\therefore gcd(b,a-b\times k)=1$

得证。

那么对于一个小于 $m!$ 且与 $m!$ 互质的数$x$，$x+1\times m!$，$x+2\times m!$ ......都与 $m!$ 互质。

因为$n>=m$，所以$n!$是$m!$的倍数，那么$1-n!$ 中与 $m!$ 互质的数的个数，根据上面的结论，很显然就是：

$$
res=φ(m!)\times \frac{n!}{m!}=m!\times \prod\limits_{i=1}^{k}\left (\dfrac{p_i-1}{p_i}\right)\times \frac{n!}{m!}=n!\times \prod\limits_{i=1}^{k}\left (\dfrac{p_i-1}{p_i}\right)
$$

我们用$ans$数组记录最后的式子的右边的东西，$ans[i]$表示$k$到$i$时右边东西的值。$jc[i]$表示$i!$。$ny[i]$表示$i$的逆元。

根据公式，很容易就能写出，答案$=jc[n]\times ans[m]$。预处理出三个数组，就能做到$O(1)$查询，这题就做完了，撒花~。

空间有点紧，不能都开$long \ long$，不该开的就不用开了。

```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define oo (1e18)
#define ll long long
#define LL unsigned long long
#define hh puts("")
using namespace std;
int T,top,n,m,ny[10000005],ans[10000005],pr[6000005];
bool v[10000005];
ll r,jc[10000005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
signed main(){
    scanf("%d%lld",&T,&r);
    jc[0]=jc[1]=1;
    ny[0]=ny[1]=1;
    ans[0]=ans[1]=1;
    for(int i=2;i<=10000000;i++){
        jc[i]=jc[i-1]*(ll)i%r;
        ny[i]=(ll)(r-r/i)*ny[r%i]%r;
        if(!v[i]) pr[++top]=i;
        for(int j=1;j<=top&&i*pr[j]<=10000000;j++){
            v[i*pr[j]]=1;
            if(i%pr[j]==0) break;
        }
    }
    for(int i=2;i<=10000000;i++){
        ans[i]=ans[i-1];
        if(!v[i]) ans[i]=(ll)ans[i]%r*(i-1)%r*ny[i]%r;
    }
    while(T--){
        n=read(),m=read();
        ll res=(ll)ans[m]%r*jc[n]%r;
        printf("%lld\n",res);
    }
    return 0;
}
```