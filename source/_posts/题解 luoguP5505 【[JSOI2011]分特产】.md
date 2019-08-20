---
title: 题解 luoguP5505 【[JSOI2011]分特产】
date: 2019-08-20 15:01:59
top: 1
tag: 
- 题解
- Luogu
- 容斥
- 数论
---
[传送门](https://www.luogu.org/problem/P5505)

考虑容斥，$ans=(>=0$个人没分到的方案$)-(>=1$个人没分到的方案$)+(>=2$个人没分到的方案$)$...

考虑计算$>=i$个人没分到的方案，每个特产分开算，最后乘起来。根据插板法，第$k$个特产的方案为$C(a[k]+n-i-1,n-i-1)$。然后还要乘上强制哪$i$个人没分到的方案，即$C(n,i)$。

设$>=i$个人没分到的方案为$f[i]$，则$ans=\sum\limits_{i=0}^{n-1}(-1)^i\times f[i]$。 

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
int n,m,a[1005],C[2005][2005],ans;
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+ch-'0';ch=getchar();}
    return ret*ff;
}
void write(int x){
    if(x<0){x=-x;putchar('-');}
    if(x>9) write(x/10);
    putchar(x%10+48);
}
int work(int x){
    x=n-x;
    int res=1;
    for(int i=1;i<=m;i++) res=res*C[a[i]+x-1][x-1]%mo;
    return res*C[n][n-x]%mo;    
}
signed main(){
    n=read(),m=read();
    for(int i=1;i<=m;i++) a[i]=read();
    for(int i=0;i<=2000;i++) C[i][0]=1;
    for(int i=1;i<=2000;i++)
        for(int j=1;j<=i;j++)
            C[i][j]=(C[i-1][j]+C[i-1][j-1])%mo;
    for(int i=0;i<=n-1;i++){//>=i个人没分到的方案 
        if(i&1) ans=(ans-work(i)+mo)%mo;
        else ans=(ans+work(i))%mo;
    }
    write(ans);
    return 0;
}
```