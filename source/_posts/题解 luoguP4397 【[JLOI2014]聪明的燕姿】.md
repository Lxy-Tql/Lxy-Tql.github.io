---
title: 题解 luoguP4397 【[JLOI2014]聪明的燕姿】
date: 2019-03-29 12:53:58
top: 1
tag: 
- 题解
- Luogu
- 搜索
- 数论
---
[传送门](https://www.luogu.org/problemnew/show/P4397)

给你一个数$S$，求约数和等于$S$的数。

好像也没什么好说的，主要就两个式子：

1、整数的唯一分解定理

![](https://i.loli.net/2019/03/29/5c9daab21c74d.png)

2、一个数的所有约数和

![](https://i.loli.net/2019/03/29/5c9daedb4a11e.png)

然后发现枚举选了哪些质数，以及这些质数的指数，等于得到了$x$，判断$S$是否符合条件即可。暴力枚举肯定会$T$，那就搜索，因为搜索可以在条件满足时再进入下一层，效率肯定大于枚举。

搜索需要三个参数，$now$，$x$，$s$，分别表示：还剩多少能够分解，当前枚举第$x$个质数，当前是哪个数。

我们知道，搜索满足条件时，就可以得到答案。那么，什么叫满足条件？有两种：

1、若当前数$now$可表示成一个并未搜索过的质数与$1$的和$($设这个质数为$p$，其实就是$p^{0}+p^{1}$ $)$，则$s$与$p$的乘积可以成为答案。

$update$：为什么要这样判？因为我们要处理的数$S$是一个大整数，最大为$2\times 1e9$，显然如果它是一个特别大的质数和另一个数的乘积，我们是无法搜索到那个特别大的质数的，筛的时候也筛不到。但我们知道，对于一个数$S$，大于$\sqrt{S}$的质因数最多只有一个。所以我们分解$S$的时候，只要处理小于等于$\sqrt{S}$的那部分，然后判断剩下的是不是个大质数即可。

2、$now=1$，即当前的数不能再分解。则当前的数$s$可以成为答案。

主要的核心还是搜索，写了注释便于理解：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define oo (1e18)
#define int long long
#define LL unsigned long long
#define hh puts("")
using namespace std;
int pr[100005],top=0,ans[100005],cnt;
bool v[100005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline bool pd(int x){
    if(x==1) return 0;
    for(int i=2;i*i<=x;i++) if(x%i==0) return 0;
    return 1;
}
void dfs(int now,     int x,      int s){
// 还剩多少能够分解  第x个质数  当前是哪个数
    if(now==1){
        ans[++cnt]=s;
        return;
    }
    if(pd(now-1)&&now>pr[x]) ans[++cnt]=s*(now-1);
    for(int i=x;pr[i]*pr[i]<=now;i++){//枚举下一个选哪个质数 
        int t=pr[i];//t为次方和的最后一个数
        int sum=pr[i]+1;//sum为总次方和 
        for(;sum<=now;t*=pr[i],sum+=t) 
            if(now%sum==0) 
                dfs(now/sum,i+1,s*t);
    }
}
signed main(){
    memset(v,1,sizeof(v));
    v[1]=0;
    for(int i=2;i<=100000;i++){
        if(v[i]) pr[++top]=i;
        for(int j=1;j<=top&&pr[j]*i<=100000;j++){
            v[i*pr[j]]=0;
            if(i%pr[j]==0) break;
        }
    }
    int x;
    while(scanf("%lld",&x)!=EOF){
        cnt=0;
        dfs(x,1,1);
        sort(ans+1,ans+cnt+1);
        printf("%lld\n",cnt);
        for(int i=1;i<=cnt;i++) printf("%lld ",ans[i]);
        if(cnt) hh;
    }
    return 0;
}
```
