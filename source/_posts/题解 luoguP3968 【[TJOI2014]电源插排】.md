---
title: 题解 luoguP3968 【[TJOI2014]电源插排】
date: 2019-08-19 13:52:49
top: 1
tag: 
- 题解
- Luogu
- 平衡树
- 树状数组
---
[传送门](https://www.luogu.org/problem/P3968)

只能说$STL$大法吼啊！

对于插入操作，用一个$multiset$，以$len$为第一关键字，右端点$r$为第二关键字排序，直接取出$begin()$即可，然后把分裂所得的左右两端重新加入。

对于删除操作，记录一下这个编号上一次插入进来的位置，我们再开一个以左端点$l$为关键字的$multiset$，然后$multiset$内二分找到该位置左右的两端区间然后合并，再插入$multiset$即可。

注意，我们操作了其中一个$multiset$，另一个中的元素也要更新，时刻保持两个$multiset$内元素相同。删除操作直接用$multiset$的$erase$和$find$函数。

对于输出答案，我是把询问以及操作离线下来，然后离散化用树状数组计算，实现也比较方便。

代码中$s1,node1$是上述第一个$multiset$，$s2,node2$是上述第二个$multiset$。

$Code \ Below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define int long long
#define hh puts("")
#define pc putchar
#define ite multiset<node2>::iterator
//#define getchar() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
//char buf[1<<21],*p1=buf,*p2=buf;
using namespace std;
int n,m,q,tr[1000005],tot,b[1000005],cnt,top;
struct node1{
    int l,r,len;
    friend bool operator < (node1 A,node1 B){
        return A.len>B.len||(A.len==B.len&&A.r>B.r);
    }
};
struct node2{
    int l,r,len;
    friend bool operator < (node2 A,node2 B){
        return A.l<B.l;
    }
};
struct work{
    int pos,v,tm;
}a[1000005];
struct que{
    int l,r,tm;
}qs[1000005];
multiset<node1> s1;
multiset<node2> s2;
map<int,int> ma;
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
int lowbit(int x){
    return x&(-x);
}
void add(int x,int v){
    while(x<=m){
        tr[x]+=v;
        x+=lowbit(x);
    }
}
int query(int x){
    int res=0;
    while(x){
        res+=tr[x];
        x-=lowbit(x);
    }
    return res;
}
signed main(){
    n=read(),q=read();
    s1.insert((node1){1,n,n});
    s2.insert((node2){1,n,n});
    for(int i=1;i<=q;i++){
        int x=read();
        if(x==0){
            int l=read(),r=read();
            b[++cnt]=l,b[++cnt]=r;
            qs[++tot]=(que){l,r,i};
        }
        else{
            if(ma[x]){
                int pos=ma[x];
                a[++top]=(work){pos,-1,i};
                ma[x]=0;
                ite R=s2.lower_bound((node2){pos,0,0});
                ite L=--R;
                ++R;
                node2 LL=*L,RR=*R;
                node2 nw=(node2){LL.l,RR.r,RR.r-LL.l+1};
                s2.erase(R);
                s2.erase(L);
                s2.insert(nw);
                s1.erase(s1.find((node1){LL.l,LL.r,LL.len}));
                s1.erase(s1.find((node1){RR.l,RR.r,RR.len}));
                s1.insert((node1){nw.l,nw.r,nw.len});
            }
            else{
                node1 t=*s1.begin();
                s1.erase(s1.find(t));
                s2.erase(s2.find((node2){t.l,t.r,t.len}));
                int l=t.l,r=t.r,mid=(l+r+1)/2;
                ma[x]=mid;
                b[++cnt]=mid;
                a[++top]=(work){mid,1,i};
                s1.insert((node1){l,mid-1,mid-l});
                s1.insert((node1){mid+1,r,r-mid});
                s2.insert((node2){l,mid-1,mid-l});
                s2.insert((node2){mid+1,r,r-mid});
            }
        }
    }
    sort(b+1,b+cnt+1);
    m=unique(b+1,b+cnt+1)-b-1;
    int now=1;
    for(int i=1;i<=tot;i++){
        while(now<=top&&a[now].tm<=qs[i].tm){
            a[now].pos=lower_bound(b+1,b+m+1,a[now].pos)-b;
            add(a[now].pos,a[now].v);
            now++;
        }
        qs[i].l=lower_bound(b+1,b+m+1,qs[i].l)-b;
        qs[i].r=lower_bound(b+1,b+m+1,qs[i].r)-b;
        write(query(qs[i].r)-query(qs[i].l-1)),hh;
    }
    return 0;
}
```