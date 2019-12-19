---
title: 题解 CF1067A 【Array Without Local Maximums】
date: 2019-12-19 21:05:39
top: 1
tag: 
- 题解
- Codeforces
- 动态规划
---
[传送门](https://www.luogu.com.cn/problem/CF1067A)

记$f[i][j][0/1]$表示前$i$个数，第$i$个数取$j$，左边一个数是否大于等于这个数（满足条件为$1$，否则为$0$）的方案数。

转移时，如果这一位为$-1$，枚举这一位的数字$1\sim200$，枚举上一位的数字$1\sim 200$转移。

代码如下：
```cpp
for(int j=1;j<=200;j++){//当前位
    for(int k=1;k<=200;k++){//上一位 
        if(k==j) f[i][j][1]=(f[i][j][1]+f[i-1][k][0]+f[i-1][k][1])%mo;
        if(k>j) f[i][j][1]=(f[i][j][1]+f[i-1][k][1])%mo;
        if(k<j) f[i][j][0]=(f[i][j][0]+f[i-1][k][0]+f[i-1][k][1])%mo;
    } 
}
```
如果这一位不为$-1$，省去当前位的枚举。

可以发现这样转移是$O(40000\times n)$的，无法通过。

观察到从上一位转移过来的$f[i-1][k][0/1]$是连续的一段，那么前缀和优化即可。

复杂度$O(200\times n)$。

~~我为什么要写这么水的题的题解？？既然写了那就交吧。~~

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
const int N=100005,M=205;
int n,a[N],f[N][M][2],ans,pre[M][2],suf[M][2];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
void write(int x){if(x<0){x=-x,pc('-');}if(x>9) write(x/10);pc(x%10+48);}
void writeln(int x){write(x),hh;}
void writesp(int x){write(x),pc(' ');}
signed main(){
    n=read();
    for(int i=1;i<=n;i++) a[i]=read();
    if(a[1]==-1) for(int i=1;i<=200;i++) f[1][i][0]=1;
    else f[1][a[1]][0]=1;
    for(int j=1;j<=200;j++){
        pre[j][0]=(pre[j-1][0]+f[1][j][0])%mo;
        pre[j][1]=(pre[j-1][1]+f[1][j][1])%mo;
    }
    for(int j=200;j>=1;j--){
        suf[j][0]=(suf[j+1][0]+f[1][j][0])%mo;
        suf[j][1]=(suf[j+1][1]+f[1][j][1])%mo;
    }
    for(int i=2;i<=n;i++){
        if(a[i]==-1){
            for(int j=1;j<=200;j++){
                f[i][j][1]=(f[i][j][1]+f[i-1][j][0]+f[i-1][j][1])%mo;
                f[i][j][1]=(f[i][j][1]+suf[j+1][1])%mo;
                f[i][j][0]=(f[i][j][0]+pre[j-1][0]+pre[j-1][1])%mo;
            }
        }
        else{
            f[i][a[i]][1]=(f[i][a[i]][1]+f[i-1][a[i]][0]+f[i-1][a[i]][1])%mo;
            f[i][a[i]][1]=(f[i][a[i]][1]+suf[a[i]+1][1])%mo;
            f[i][a[i]][0]=(f[i][a[i]][0]+pre[a[i]-1][0]+pre[a[i]-1][1])%mo;
        }
        for(int j=1;j<=200;j++){
            pre[j][0]=(pre[j-1][0]+f[i][j][0])%mo;
            pre[j][1]=(pre[j-1][1]+f[i][j][1])%mo;
        }
        for(int j=200;j>=1;j--){
            suf[j][0]=(suf[j+1][0]+f[i][j][0])%mo;
            suf[j][1]=(suf[j+1][1]+f[i][j][1])%mo;
        }
    }
    for(int i=1;i<=200;i++) ans=(ans+f[n][i][1])%mo;
    write(ans);
    return 0;
}
```
