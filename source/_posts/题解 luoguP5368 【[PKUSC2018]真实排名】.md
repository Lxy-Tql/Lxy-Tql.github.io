---
title: 题解 luoguP5368 【[PKUSC2018]真实排名】
date: 2019-12-15 20:39:58
top: 1
tag: 
- 题解
- Luogu
- 组合数学
---
[传送门](https://www.luogu.com.cn/problem/P5368)

签到题。

分两种情况，一种自己不翻倍，一种翻倍。

**不翻倍的情况：**

对于一个数，设 不翻倍大于等于它的个数$+$翻倍了还小于它的个数 这两者的和为$sum$，那么方案为$C_{sum}^k$。

**翻倍的情况：**

对于一个数，设有$x$个数翻倍后大于等于它（它指的是这个数翻倍后的值，下同），$y$个数大于等于它，$z$个数翻倍后还是小于它，以及这个数翻倍后增长的排名$p$。那么方案为$C_{x}^{p}\times C_{y+z}^{k-p-1}$。

注意还有$a_i=0$要特判，方案数为$C_{n}^{k}$。

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
int n,k,ans[N],rk[N],l[N],jc[N],ny[N];
struct node{
    int v,id;
    friend bool operator < (node A,node B){
        return A.v<B.v;
    }
}a[N];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
void write(int x){if(x<0){x=-x,pc('-');}if(x>9) write(x/10);pc(x%10+48);}
void writeln(int x){write(x),hh;}
void writesp(int x){write(x),pc(' ');}
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
    if(x<y||y<0) return 0;
    return jc[x]*ny[y]%mo*ny[x-y]%mo;
}
signed main(){
    n=read(),k=read();
    for(int i=1;i<=n;i++) a[i].v=read(),a[i].id=i;
    sort(a+1,a+n+1);
    a[0].v=-1;
    for(int i=1;i<=n;i++){
        if(a[i].v==a[i-1].v) rk[i]=rk[i-1],l[i]=l[i-1];
        else rk[i]=n-i+1,l[i]=i;
    }
    jc[0]=1;
    for(int i=1;i<=n;i++) jc[i]=jc[i-1]*i%mo;
    ny[n]=ksm(jc[n],mo-2);
    for(int i=n-1;i>=0;i--) ny[i]=ny[i+1]*(i+1)%mo;
    for(int i=1;i<=n;i++){
        if(a[i].v==0){
            ans[a[i].id]=C(n,k);
            continue;
        }
        int x=n-l[i],y=upper_bound(a+1,a+n+1,(node){(a[i].v+1)/2-1,0})-a-1,id=a[i].id,z;
        ans[id]=C(x+y,k);
        int pos=lower_bound(a+1,a+n+1,(node){a[i].v*2,0})-a,zhang=rk[i]-rk[pos]-1;
        x=pos-1-l[i],y=n-pos+1,z=l[i]-1;
        ans[id]=(ans[id]+C(x,zhang)*C(y+z,k-zhang-1)%mo)%mo;
    }
    for(int i=1;i<=n;i++) writeln(ans[i]);
    return 0;
}
```
