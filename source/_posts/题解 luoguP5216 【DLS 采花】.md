---
title: 题解 luoguP5216 【DLS 采花】
date: 2019-11-12 15:11:16
top: 1
tag: 
- 题解
- Luogu
- 组合数学
- 逆元
---
[传送门](https://www.luogu.org/problem/P5216)

显然每个数的贡献可以单独算，即这个数的值$\times$方案数。

现在的问题就是，对于一个数，有多少种排列，使得它的因子不在它之前。我们不需要知道因子的值，只需要知道个数，设为$x$。

方案特别好算，考虑算上它本身的$x+1$个数，先随便在数列中放，方案为$C_n^{x+1}$，考虑$x+1$个数中，它本身要放最前面，剩下$x$个随便放，即$x!$，考虑剩下的$n-x-1$个数随便放，方案为$(n-x-1)!$。

所以对于一个数，贡献为$a[i]\times C_n^{x+1}\times x!\times (n-x-1)!$。

预处理阶乘逆元即可。

$Code\ Below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define int long long
#define hh puts("")
#define pc putchar
#define mo 998244353
//#define getchar() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
//char buf[1<<21],*p1=buf,*p2=buf;
using namespace std;
const int N=100005;
int n,a[N],tong[N],gs[N],inv[N],jc[N],ans;
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
void write(int x){if(x<0){x=-x,pc('-');}if(x>9) write(x/10);pc(x%10+48);}
void writeln(int x){write(x),hh;}
void writesp(int x){write(x),pc(' ');}
void part(int id,int x){
    for(int i=1;i*i<=x;i++){
        if(x%i==0){
            gs[id]+=tong[i];
            if(i*i!=x) gs[id]+=tong[x/i];
        }
    }
    gs[id]--;
}
int ksm(int x,int y){
    int res=1;
    while(y){
        if(y&1) res=res*x%mo;
        y>>=1;
        x=x*x%mo;
    }
    return res;
}
int C(int x,int y){
    return jc[x]*inv[y]%mo*inv[x-y]%mo;
}
signed main(){
    n=read();
    for(int i=1;i<=n;i++) a[i]=read(),tong[a[i]]++;
    for(int i=1;i<=n;i++) part(i,a[i]);
    jc[0]=1;
    for(int i=1;i<=n;i++) jc[i]=jc[i-1]*i%mo;
    inv[n]=ksm(jc[n],mo-2);
    for(int i=n-1;i>=0;i--) inv[i]=inv[i+1]*(i+1)%mo;
    for(int i=1;i<=n;i++) ans=(ans+C(n,gs[i]+1)*jc[gs[i]]%mo*jc[n-gs[i]-1]%mo*a[i]%mo)%mo;
    write(ans);
    return 0;
}
```