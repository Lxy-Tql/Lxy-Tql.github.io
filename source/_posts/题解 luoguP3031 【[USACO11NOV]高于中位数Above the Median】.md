---
title: 题解 luoguP3031 【[USACO11NOV]高于中位数Above the Median】
date: 2019-06-24 08:47:22
top: 1
tag:
- 题解
- Luogu
- 树状数组
- 前缀和
---
[传送门](https://www.luogu.org/problemnew/show/P3031)

对于这种中位数的题目，按照套路，把大于等于$x$的置为$1$，小于$x$的置为$-1$，然后先计算一波前缀和。

然后，问题就转化成要找满足$sum[r]-sum[l-1]>=0$的$(l,r)$的对数。

枚举这个$l-1$，即枚举$0$到$n-1$，每次求$sum[r]>=sum[l-1]$的$r$的个数，显然我们可以用树状数组很方便的维护出来，那么这题就解决了。

注意$sum$可能为负，下标要进行整体加。

复杂度$O(n\ log \ n)$。

$Code\ below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
using namespace std;
int n,m,a[100005],b[100005],s[100005],tr[500005],tong[500005];
ll ans;
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
inline int lowbit(int x){
    return (x)&(-x);
}
inline int change(int x){
    return x+100001;
}
inline void add(int x,int v){
    while(x<=210000){
        tr[x]+=v;
        x+=lowbit(x);
    }
}
inline int query(int x){
    int res=0;
    while(x){
        res+=tr[x];
        x-=lowbit(x);
    }
    return res;
}
signed main(){
    n=read(),m=read();
    for(int i=1;i<=n;i++){
        a[i]=read();
        if(a[i]>=m) b[i]=1;
        else b[i]=-1;
    }
    add(change(0),1);
    tong[change(0)]++;
    for(int i=1;i<=n;i++){
        s[i]=s[i-1]+b[i];
        add(change(s[i]),1);
        tong[change(s[i])]++;
    }
    for(int i=0;i<n;i++){
        int t=n-i-query(change(s[i]))+tong[change(s[i])];
        ans+=t;
        add(change(s[i]),-1);
        tong[change(s[i])]--;
    }
    printf("%lld",ans);
    return 0;
}
```