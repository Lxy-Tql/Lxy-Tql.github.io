---
title: 题解 luoguP2886 【[USACO07NOV]牛继电器Cow Relays】
date: 2019-03-18 17:56:44
top: 1
tag:
- 题解
- Luogu
- 离散化
- Floyd
- 矩阵
- 最短路
---
[传送门](https://www.luogu.org/problemnew/show/P2886)

首先发现是一张连通图，而且只有$100$条边，那么点也很少

编号是乱的，先进行一波离散化，上$map$即可(~~STL大法好~~)

离散化后，转化成邻接矩阵，这个矩阵实际上表示的是$i$到$j$只经过$1$条边的最短路矩阵。这时发现，我们要求的答案是一个$st$到$ed$且只经过$n$条边的最短路矩阵。

考虑如何从小矩阵推向大矩阵：设矩阵$A$是一个任意两点经过$x$条边的最短路矩阵，设矩阵$B$是一个任意两点经过$y$条边的最短路矩阵

$A$ $\times$ $B$得到矩阵$C$，那么矩阵$C$是一个经过$(x+y)$条边的矩阵

重载乘法，因为矩阵转移时的实质为$Floyd$松弛：$C(i,j)=min(A(i,k)+B(k,j)) (1<=k<=n)$

答案就是初始矩阵的$k$次方，矩阵快速幂加速即可 
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define oo (1e18)
#define int long long
#define LL unsigned long long
#define hh puts("")
using namespace std;
int k,m,st,ed,cnt=0;
map<int,int> mp;
struct matrix{
    int ma[105][105];
    void init(){memset(ma,0x3f,sizeof(ma));}
}f,ans;
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
matrix mul(matrix a,matrix b){
    matrix res;
    res.init();
    for(int kk=1;kk<=cnt;kk++)
        for(int i=1;i<=cnt;i++)
            for(int j=1;j<=cnt;j++)
                res.ma[i][j]=min(res.ma[i][j],a.ma[i][kk]+b.ma[kk][j]);
    return res;
}
void ksm(int p){
    while(p){
        if(p&1) ans=mul(ans,f);
        p>>=1;
        f=mul(f,f);
    }
}
signed main(){
    k=read(),m=read(),st=read(),ed=read();
    f.init(),ans.init();
    for(int i=1;i<=m;i++){
        int v=read();
        int x=read(),y=read();
        if(!mp[x]) mp[x]=++cnt;
        if(!mp[y]) mp[y]=++cnt;
        f.ma[mp[x]][mp[y]]=f.ma[mp[y]][mp[x]]=min(f.ma[mp[x]][mp[y]],v);
    }
    for(int i=1;i<=cnt;i++) ans.ma[i][i]=0;
    ksm(k);
    printf("%lld",ans.ma[mp[st]][mp[ed]]);
    return 0;
}
```