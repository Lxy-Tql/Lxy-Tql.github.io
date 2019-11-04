---
title: 题解 CF173E 【Camping Groups】
date: 2019-11-04 09:37:59
top: 1
tag: 
- 题解
- Codeforces
- 主席树
- 线段树
---
[传送门](https://www.luogu.org/problem/CF173E)

数据结构~~码农~~好题。

先总结一些限制：

1.包含$x,y$的队长的地位要$\geq max(p[x],p[y])$。

2.对于$x,y$，设$age[x]\leq age[y]$，则队长的年龄限制为$[age[y]-k,age[x]+k]$，若$age[y]-k>age[x]+k$则不合法。 

首先我们预处理每个人做队长时，这个队的最大人数。这东西我们设为$can[i]$，求这个东西可以按地位排个序，然后主席树即可。

然后考虑离线做这道题，根据第一个限制，我们对于所有询问，按$max(p[x],p[y])$从大到小排序。那么做这个询问前，我们需要把$p[i]\geq max(p[x],p[y])$的每个人作为队长的$can[i]$插入到线段树上这个人年龄的位置。然后在这个询问需要满足的年龄限制$($也就是限制$2)$的范围内，查询最大的$can[i]$即可。

有了思路，代码其实就是一堆数据结构拼凑起来，注意实现细节即可。

$Code\ Below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
#define pc putchar
//#define getchar() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
//char buf[1<<21],*p1=buf,*p2=buf;
using namespace std;
const int N=100005;
int n,m,k,b[N],B,pos[N],same[N],can[N],rt[N],ans[N];
struct node{
    int p,age,id;
    friend bool operator < (node A,node B){
        return A.p<B.p;
    }
}a[N];
struct que{
    int x,y,v;
}q[N];
struct lxy{
    int l,r,id;
};
vector<lxy> g[N];
vector<int> alb[N];
struct ChairMan_Tree{
    int ls[N*40],rs[N*40],sum[N*40],cnt;
    void build(int &now,int l,int r,int x){
        now=++cnt;
        if(l==r){
            sum[now]++;
            return;
        }
        int mid=(l+r)>>1;
        if(x<=mid) build(ls[now],l,mid,x);
        else build(rs[now],mid+1,r,x);
        sum[now]=sum[ls[now]]+sum[rs[now]];
    }
    void update(int &now,int pre,int l,int r,int x){
        now=++cnt;
        ls[now]=ls[pre],rs[now]=rs[pre],sum[now]=sum[pre]+1;
        if(l==r) return;
        int mid=(l+r)>>1;
        if(x<=mid) update(ls[now],ls[pre],l,mid,x);
        else update(rs[now],rs[pre],mid+1,r,x);
    }
    int query(int now,int l,int r,int x,int y){
        if(!now) return 0;
        if(x<=l&&r<=y) return sum[now];
        int mid=(l+r)>>1,res=0;
        if(x<=mid) res+=query(ls[now],l,mid,x,y);
        if(mid+1<=y) res+=query(rs[now],mid+1,r,x,y);
        return res;
    }
}Tr;
struct Segment_Tree{
    int ls[N*40],rs[N*40],mx[N*40],cnt,rt;
    void update(int l,int r,int x,int v,int &k){
        if(!k) k=++cnt;
        if(l==r){
            mx[k]=max(mx[k],v);
            return;
        }
        int mid=(l+r)>>1;
        if(x<=mid) update(l,mid,x,v,ls[k]);
        else update(mid+1,r,x,v,rs[k]);
        mx[k]=max(mx[ls[k]],mx[rs[k]]);
    }
    int query(int l,int r,int x,int y,int k){
        if(!k) return 0;
        if(x<=l&&r<=y) return mx[k];
        int mid=(l+r)>>1,res=0;
        if(x<=mid) res=max(res,query(l,mid,x,y,ls[k]));
        if(mid+1<=y) res=max(res,query(mid+1,r,x,y,rs[k]));
        return res;
    }
}T;
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
    for(int i=1;i<=n;i++){
        a[i].p=read();
        a[i].id=i;
        b[i]=a[i].p;
    }
    for(int i=1;i<=n;i++) a[i].age=read();
    sort(b+1,b+n+1);
    B=unique(b+1,b+n+1)-b-1;
    for(int i=1;i<=n;i++) a[i].p=lower_bound(b+1,b+B+1,a[i].p)-b;
    sort(a+1,a+n+1);
    Tr.build(rt[1],1,1e9,a[1].age);
    for(int i=2;i<=n;i++) Tr.update(rt[i],rt[i-1],1,1e9,a[i].age);
    int R;
    for(int i=1;i<=n;i++){//相同地位的我们在查的时候要找到最右边的人
        R=i;
        while(R<=n&&a[R].p==a[i].p) R++;
        R--;
        for(int j=i;j<=R;j++) same[j]=R;
        i=R;
    }
    for(int i=1;i<=n;i++){
        pos[a[i].id]=i;
        can[i]=Tr.query(rt[same[i]],1,1e9,max(1,a[i].age-k),min((int)1e9,a[i].age+k));
        alb[a[i].p].push_back(i);
    }
    m=read();
    for(int i=1;i<=m;i++){
        q[i].x=read();
        q[i].y=read();
        q[i].x=pos[q[i].x];
        q[i].y=pos[q[i].y];
        q[i].v=max(a[q[i].x].p,a[q[i].y].p);
        if(a[q[i].x].age>a[q[i].y].age) swap(q[i].x,q[i].y);
        g[q[i].v].push_back((lxy){a[q[i].y].age-k,a[q[i].x].age+k,i});
    }
    for(int i=B;i>=1;i--){
        for(int j=0;j<alb[i].size();j++){
            int id=alb[i][j];
            int age=a[id].age,val=can[id];//age这个点插入val的贡献 
            T.update(1,1e9,age,val,T.rt);
        }
        for(int j=0;j<g[i].size();j++){
            int l=g[i][j].l,r=g[i][j].r,id=g[i][j].id;
            if(l<=r) ans[id]=T.query(1,1e9,l,r,T.rt);
            if(!ans[id]) ans[id]=-1;
        }
    }
    for(int i=1;i<=m;i++) writeln(ans[i]);
    return 0;
}
```