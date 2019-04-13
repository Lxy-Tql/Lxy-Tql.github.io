---
title: 题解 luoguP2161 【[SHOI2009]会场预约】
date: 2019-03-08 14:50:44
top: 1
tag: 
- 题解
- Luogu
- 线段树
---
[传送门](https://www.luogu.org/problemnew/show/P2161)

~~线段树好题~~ 。
转换题意。将操作看成染色：操作$A$，即统计一段区间内颜色的种类数，并将这个区间修改为同一个颜色。操作$B$，统计整颗线段树上还剩下多少种颜色。

考虑如何用线段树实现这一操作：对于操作$A$，明显就是一个查询$->$覆盖的操作。对于查询，考虑在修改时进行查询。我们用一个$col[k]$数组维护一段区间的颜色是否为同一个。若为同一个，我们覆盖掉，并使$delans$++$(delans$表示拒绝掉的预约数$)$，否则，分左右两段递归修改。答案即$delans$。

这时有一个问题：若一种颜色一段在修改的区间内，一段在修改的区间外，怎么处理？根据题意要求：对于新的预约操作，只要与先前预约有冲突，就全部拒绝掉。那就再设计一个$del$数组，记录一种颜色是否被删掉，就可以做到只要一种颜色有一段在区间内，就能保证它被全部删掉。

操作$B$相对简单很多，维护一个全局颜色数$ans$，每次$A$操作染色，显然先使$ans$++$($多一种颜色$)$，在染色时，每拒绝掉一个预约，$ans$- -即可。然后操作$B$就要求输出$ans$。

完结撒花~~

哦对，线段树标配$tag$数组就代表该区间的颜色，$tag$为$0$时表示没有颜色，无需下传。
下传时的细节：既然下传了，那么显然区间不是同色，$col[k]$赋为$0$。显然原因：下传操作的进行，是因为当前区间不完全在修改区间范围内$($否则就直接$change$并且$return$了$)$，那么当前区间一部分在修改区间范围内，一部分不在，显然不同色。
代码：
```cpp
#include<bits/stdc++.h>
#define ll long long
#define LL unsigned long long
#define ls(x) x<<1
#define rs(x) x<<1|1
#define hh puts("")
using namespace std;
int n,col[400005],maxx,cnt,ans,del_cnt=0;
int del[400005],tag[400005];
struct Q{
    int l,r,opt;
}q[200005];
void build(int l,int r,int k){
    col[k]=1;//col[k]表示线段树中的k区间颜色都相同(把预约看成染色)
    if(l==r) return;
    int mid=(l+r)>>1;
    build(l,mid,ls(k));
    build(mid+1,r,rs(k));
}
void push_down(int l,int r,int k){
    col[k]=0;
    if(!tag[k]) return;
    tag[ls(k)]=tag[k];
    tag[rs(k)]=tag[k];
    tag[k]=0;
}
void change(int l,int r,int v,int k){
    if(col[k]){
        if(tag[k]&&!del[tag[k]]){            
            del_cnt++;
            ans--;            
        }
        del[tag[k]]=1;
        tag[k]=v;
        return;
    }
    int mid=(l+r)>>1;
    change(l,mid,v,ls(k));
    change(mid+1,r,v,rs(k));
    col[k]=1,tag[k]=v;
}
void update(int l,int r,int x,int y,int v,int k){//将x~y区间内所有颜色都变成v 
    if(x<=l&&r<=y){
        change(l,r,v,k);
        return;
    }
    push_down(l,r,k);
    int mid=(l+r)>>1;
    if(x<=mid) update(l,mid,x,y,v,ls(k));
    if(mid+1<=y) update(mid+1,r,x,y,v,rs(k));
}
signed main(){
    scanf("%d",&n);
    for(int i=1;i<=n;i++){
        char rd[5];
        scanf("%s",rd+1);
        if(rd[1]=='A'){
            q[i].opt=1;
            scanf("%d%d",&q[i].l,&q[i].r);
            maxx=max(maxx,q[i].r);
        }
        else q[i].opt=2;
    }
    build(1,maxx,1);
    for(int i=1;i<=n;i++){
        if(q[i].opt==1){
            ans++;
            del_cnt=0;
            update(1,maxx,q[i].l,q[i].r,++cnt,1);
            printf("%d\n",del_cnt);
        }
        else printf("%d\n",ans);
    }
    return 0;
}
```