---
title: 题解 BZOJ4260 【Codechef REBXOR】
date: 2019-03-27 20:50:51
top: 1
tag: 
- 题解
- bzoj
- Trie树
---
[传送门](https://www.lydsy.com/JudgeOnline/problem.php?id=4260)

题目要求一个序列中的两个子段，使这两个子段每段的异或和加起来值最大。

说到异或，我就想到~~开花~~$Trie$字典树。

先考虑怎么求一个$l$到$r$子段的异或和$($异或和：所有数异或起来的值$)$：

$$
a_l\oplus a_{l+1}\oplus...\oplus a_{r-1} \oplus a_{r}=(a_1\oplus a_2\oplus...\oplus a_{r-1} \oplus a_{r})\oplus(a_1\oplus a_2\oplus...\oplus a_{l-2} \oplus a_{l-1})
$$

所以处理出异或前缀和，不断加入$01Trie$树求解即可。

考虑到要求两个子段，用$ls[i],rs[i]$分别表示$1-i$的最大异或子段，$i-n$的最大异或子段。最后扫一遍取$max$即可。
代码不长：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define lowbit(x) (x)&(-x)
#define oo (1e18)
#define ll long long
#define LL unsigned long long
using namespace std;
int n,a[400005],s[400005],ls[400005],rs[400005],ch[12500000][2],cnt;
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline void insert(int x){
    int now=0;
    for(int i=30;i>=0;i--){
        int t=(x&(1<<i))?1:0;
        if(!ch[now][t]) ch[now][t]=++cnt;
        now=ch[now][t];
    }
}
inline int find(int x){
    int now=0,res=0;
    for(int i=30;i>=0;i--){
        int t=(x&(1<<i))?0:1;
        if(ch[now][t]){
            now=ch[now][t];
            res+=(1<<i);
        }
        else now=ch[now][!t];
    }
    return res;
}
signed main(){
    n=read();
    for(int i=1;i<=n;i++) a[i]=read();
    ls[1]=a[1];
    insert(a[1]);
    for(int i=2;i<=n;i++){
        ls[i]=max(ls[i-1],find(a[i]));
        insert(a[i]);
    }
    memset(ch,0,sizeof(ch));
    cnt=0;
    rs[n]=a[n];
    insert(a[n]);
    for(int i=n-1;i>=2;i--){
        rs[i]=max(rs[i+1],find(a[i]));
        insert(a[i]);
    }
    int ans=0;
    for(int i=1;i<=n-1;i++) ans=max(ans,ls[i]+rs[i+1]);
    printf("%d",ans);
    return 0;
}
```