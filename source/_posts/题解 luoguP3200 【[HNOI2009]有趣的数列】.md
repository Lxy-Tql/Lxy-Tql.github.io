---
title: 题解 luoguP3200 【[HNOI2009]有趣的数列】
date: 2019-04-01 13:33:05
top: 1
tag:
- 题解
- Luogu
- 卡特兰数
- 数论
---
[传送门](https://www.luogu.org/problemnew/show/P3200)

~~很好的一道思维题~~。警告：文字较多，没有耐心者勿入。

首先我们命名$a\_1,a\_3,...,a\_{2n-1}$为奇数位，其余为偶数位。观察题目条件：奇数位与偶数位上的数字都满足从左到右递增，相邻的$a\_{2i-1},a\_{2i}$满足$a\_{2i-1}<a\_{2i}$。

首先很容易发现，一个偶数位上的数，比它左边的所有偶数位上的数要大，每个偶数位上的数又比它左相邻奇数位上的数要大。这两条信息，我们可以得出，一个偶数位上的数比它左边所有数都要大，那么再概括一下，就是一个偶数位上的数，大于等于这个偶数位的下标。

这个结论并不够我们来得出最后的答案，我们还需要一些结论。

因为数字从左到右，无论奇数位偶数位都满足递增，那么，我们考虑假如我们按$1-2n$的顺序一个一个放数字，我们应该放在哪里？

很容易发现，我们应该放在最靠左的能放的奇数位或者偶数位上，这样才能保证满足递增。

那还有什么限制呢？我们设已经放了$1-x$的数，$x_1$个放在了奇数位上，$x_2$个放在了偶数位上$(x_1+x_2=x)$。我们想起前面得出的关于偶数位上放数的结论：一个偶数位上的数，大于等于这个偶数位的下标。那什么时候会小于呢？猜想+估摸一下，感觉是$x_2>x_1$？？

猜想是很**的，作为一个严谨的人，让我们来简单的证一下。

反证：

假设当前情况为$x_2>x_1$，那么显然$x_2>x/2$，同时最后一个偶数位的下标是$2\times x_2$。

因为我们总共只有$x$个数，但由$x_2>x/2$我们得知，$2\times x_2>x$，所以最后一个偶数位不管放什么，都不满足条件，假设不成立。

所以得出结论，按从小到大的顺序放到任意一个数$x$，都满足放在偶数位上的数字个数小于等于放在奇数位上的数字个数。

想到什么？[This](https://www.luogu.org/problemnew/show/P1641)

这不是完全一样的卡特兰数吗？但这道题好像更麻烦一点，模数不是质数。那么就需要一些求卡特兰数的技巧，参考别的题解即可，几乎都讲了。

代码：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define oo (1e18)
#define int long long
#define LL unsigned long long
#define hh puts("")
using namespace std;
int n,mo,cnt[2000005],pr[2000005],mn[2000005],top,ans=1;
bool v[2000005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline int ksm(int x,int y){
    int res=1;
    while(y){
        if(y&1) res=res*x%mo;
        y>>=1;
        x=x*x%mo;
    }
    return res;
}
signed main(){
    n=read(),mo=read();
    memset(v,1,sizeof(v));
    v[1]=0;
    for(int i=2;i<=2*n;i++){
        if(v[i]){
            pr[++top]=i;
            mn[i]=i;
        }
        for(int j=1;j<=top&&pr[j]*i<=2*n;j++){
            v[pr[j]*i]=0;           
            if(i%pr[j]==0) break;
            mn[pr[j]*i]=pr[j];
        }
    }
    for(int i=1;i<=n;i++) cnt[i]=-1;
    for(int i=n+2;i<=2*n;i++) cnt[i]=1;
    for(int i=2*n;i>=2;i--){
        if(mn[i]<i){
            cnt[mn[i]]+=cnt[i];
            cnt[i/mn[i]]+=cnt[i];
        }
    }
    for(int i=2;i<=2*n;i++)
        if(mn[i]==i)
            ans=ans*ksm(i,cnt[i])%mo;
    printf("%lld",ans);
    return 0;
}
```