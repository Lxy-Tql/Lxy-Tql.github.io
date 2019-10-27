---
title: 题解 luoguP4359 【[CQOI2016]伪光滑数】
date: 2019-10-28 07:58:01
top: 1
tag:
- 题解
- Luogu
- 数论
- 堆
---
[传送门](https://www.luogu.org/problem/P4359)

首先我们要发现一个性质，就是**最大的**伪光滑数所有质因数相同。如果一个合法的伪光滑数有不相同的质因数，我们把小的质因数全部换成最大的，需要满足的式子$a_k^k\leq N$中，$k$没有变化，所以这个数仍旧合法，却比原来的数大。

观察到要求的第$K$大的$K$较小，那么我们用堆维护。每次取出最大值，如果这个数最大质因数的幂次大于$1$，那么把其中一个最大质因数换成较小的扔进堆里。由于最大质因数和分解的项数$k$不变，所以始终满足$a_k^k\leq N$。同时也很容易发现这样枚举是没有遗漏和重复的。

$Code\ Below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define int long long
#define hh puts("")
#define pc putchar
//#define getchar() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
//char buf[1<<21],*p1=buf,*p2=buf;
using namespace std;
int n,k;
int pr[105]={0,2,3,5,7,11,13,17,19,23,29,31,37,41,43,47,53,
59,61,67,71,73,79,83,89,97,101,103,107,109,113,127};//31个
struct node{
    int val,p,mi,lim;//值,最大质数,幂次,下个数的限制 
    friend bool operator < (node A,node B){
        return A.val<B.val;
    }
};
priority_queue<node> q; 
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
    n=read(),k=read();
    for(int i=1;i<=31;i++){
        int now=pr[i];
        for(int j=1;now<=n;j++,now=now*pr[i])
            q.push((node){now,pr[i],j,i-1});
    }
    while(k--){
        node now=q.top();
        q.pop();
        if(!k){
            write(now.val);
            return 0;
        }
        if(now.mi>1)
            for(int i=1;i<=now.lim;i++)
                q.push((node){now.val/now.p*pr[i],now.p,now.mi-1,i});
    }
    return 0;
}
```