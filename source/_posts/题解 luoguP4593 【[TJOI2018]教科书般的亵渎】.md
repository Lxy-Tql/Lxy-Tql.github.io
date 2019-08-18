---
title: 题解 luoguP4593 【[TJOI2018]教科书般的亵渎】
date: 2019-08-18 08:20:27
top: 1
tag: 
- 题解
- Luogu
- 多项式
- 拉格朗日插值
---
[传送门](https://www.luogu.org/problem/P4593)

先算出所需亵渎个数$k$，观察就可以发现$k=m+1$，有一个小细节，如果从$n$开始有一段连续的空位，应该把它去掉，因为不会需要多余的亵渎。

我们计算每一次亵渎的贡献，第一次亵渎我们认为是在$0$位置。显然第一次的贡献是$\sum\limits_{i=1}^{n}i^k$ $-$ 空位的贡献。

之后我们考虑在一个空位上使用亵渎，设空位为$p$，那么有贡献的区间为$p+1 \sim n$。贡献为$\sum\limits_{i=1}^{n-p}i^k$。

最后我们减去空位多算的贡献即可。

考虑计算$\sum\limits_{i=1}^{n}i^k$，可利用拉格朗日插值，参考[这里](https://www.luogu.org/problem/CF622F)

$Code\ Below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define int long long
#define hh puts("")
#define mo 1000000007
using namespace std;
int n,m,a[55],k,f[55],pre[55],suf[55],fac[55],ans;
map<int,int> ma;
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+ch-'0';ch=getchar();}
    return ret*ff;
}
void write(int x){
    if(x>9) write(x/10);
    putchar(x%10+48);
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
inline int calc(int p){
    if(p<=k+2) return f[p];
    int res=0;
    pre[0]=1;
    for(int i=1;i<=k+2;i++) pre[i]=pre[i-1]*(p-i)%mo;
    suf[k+3]=1;
    for(int i=k+2;i>=1;i--) suf[i]=suf[i+1]*(p-i)%mo;
    for(int i=1;i<=k+2;i++){
        int x=pre[i-1]*suf[i+1]%mo;
        int fu=((k+2-i)&1)?-1:1;
        int y=fac[i-1]*fac[k+2-i]*fu%mo;
        res=(res+f[i]*x%mo*ksm(y,mo-2)%mo)%mo;
    }
    return (res+mo)%mo;
}
signed main(){
    fac[0]=1;
    for(int i=1;i<=52;i++) fac[i]=fac[i-1]*i%mo;
    int T=read();
    while(T--){
        n=read(),m=read();
        k=m+1;
        ma.clear();
        for(int i=1;i<=m;i++) a[i]=read(),ma[a[i]]=1;
        sort(a+1,a+m+1);
        while(ma[n]) n--,k--,m--;
        for(int i=1;i<=k+2;i++) f[i]=(f[i-1]+ksm(i,k))%mo;
        ans=calc(n);
        for(int i=1;i<=m;i++) ans=(ans-ksm(a[i],k))%mo;
        for(int i=1;i<=m;i++) ans=(ans+calc(n-a[i]))%mo;
        for(int i=1;i<=m;i++)
            for(int j=i-1;j>=1;j--)
                ans=(ans-ksm(a[i]-a[j],k))%mo;
        write((ans+mo)%mo),hh;
    }
    return 0;
}
```