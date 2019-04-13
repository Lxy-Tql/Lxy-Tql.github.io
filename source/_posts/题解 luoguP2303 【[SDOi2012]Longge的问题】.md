---
title: 题解 luoguP2303 【[SDOi2012]Longge的问题】
date: 2019-04-08 19:06:18
top: 1
tag:
- 题解
- Luogu
- 欧拉函数
- 数论
---
[传送门](https://www.luogu.org/problemnew/show/P2303)

好水的蓝题啊，~~少数没有看题解做的题~~

题面简洁明了：求$\sum\limits_{i=1}^{n}gcd(i,n)$

设$gcd(i,n)=d$，则$gcd(i/d,n/d)=1$

$d$显然就是$n$的因数

我们对于每个$d$，要求有多少$i$使得$gcd(i/d,n/d)=1$，设求出有$x$个$i$，那么对答案的贡献就是$d \times x$。为什么是这些贡献？很显然，这些$i$与$n$的$gcd$就是$d$，共$x$个这样的$i$，所以是$d \times x$。

因为满足$gcd(i/d,n/d)=$1的$i/d$的个数就是与$n/d$互质的数，每个$i/d$又对应一个$i$，个数就是$φ(n/d)$。前面说了$d$是$n$的因数，我们枚举所有因数，累加答案即可。

对于每个$φ(n/d)$，$\sqrt{n}$求即可。

代码：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define oo (1e18)
#define int long long
#define LL unsigned long long
#define hh puts("")
using namespace std;
int ans,n;
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline int getphi(int x){
    int res=x;
    for(int i=2;i*i<=x;i++){
        if(x%i==0){
            res=res/i*(i-1);
            while(x%i==0) x/=i; 
        }
    }
    if(x>1) res=res/x*(x-1);
    return res;
}
signed main(){
    n=read();
    int sq=sqrt(n);
    for(int i=1;i<=sq;i++){
        if(n%i==0){
            ans+=getphi(n/i)*i;
            if(i*i!=n) ans+=getphi(i)*(n/i);
        }
    }
    printf("%lld",ans);
    return 0;
}
```