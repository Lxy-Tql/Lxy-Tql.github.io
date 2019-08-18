---
title: 题解 luoguP3960 【列队】
date: 2019-08-18 08:30:37
top: 1
tag: 
- 题解
- Luogu
- 平衡树
- 线段树
---
[传送门](https://www.luogu.org/problem/P3960)

$NOIP$题做的好心累。。

最后一列维护一个平衡树，每行维护一个动态开点的权值线段树。

我们给所有操作过的点一个权值，再用$ma$数组，表示值映射的编号，这样是为了在平衡树内有序。

考虑一行内经过一系列操作会变成什么样，首先本来是有序的，之后我们删除了一些点，再从行末尾插入了一些点，那么这一行的前半段还是有序的，后半段是无序的。

所以我们用权值线段树维护每一行删除了哪些节点，然后对于一个$(x,y)$的查询，如果这一行剩下的数的个数$t$大于等于要查询的$y$，我们就在权值线段树里找所有剩下的数中的第$y$大即可。否则，我们每行用一个$vector$来存储那些无序的数字，输出$vector$内第$y-t$个数即可。

然后考虑最后一列，操作很简单，就是删除排名为$x$的，然后把查询到的$(x,y)$插入最末尾，对于插入最末尾，我们直接给这个数赋一个当前最大的权值然后插进去即可，再更新一下映射数组$ma$。

代码中特判了一些$y==m$的情况。

个别变量含义：

$leave:$ 每行剩下的数的个数

$R:$ 当前的最大权值

$v[i]:$ 第$i$行的$vector$(见上)

$last:$ 上一次的答案(为了特判一些情况来减少操作)

其余均为数据结构的变量

$Code\ Below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define int long long
#define hh puts("")
//#define getchar() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
//char buf[1<<21],*p1=buf,*p2=buf;
using namespace std;
int n,m,q,last,R,ma[5000005],val[1000005],sz[1000005],cnt[1000005],size,root,rt[300005];
int ch[1000005][2],f[1000005],num,leave[300005],sum[10000005],ls[10000005],rs[10000005];
vector<int> v[300005];
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
//-------------以下splay-----------------
int chk(int x){ 
    return ch[f[x]][1]==x;
}
void clear(int x){
    f[x]=ch[x][0]=ch[x][1]=val[x]=cnt[x]=sz[x]=0;
}
void update(int x){
    if(x){
        sz[x]=cnt[x];
        if(ch[x][0]) sz[x]+=sz[ch[x][0]];
        if(ch[x][1]) sz[x]+=sz[ch[x][1]];
    }
}
void rotate(int x){
    int fa=f[x],gfa=f[fa];
    int which=chk(x);
    if(gfa) ch[gfa][chk(fa)]=x;
    f[x]=gfa;
    ch[fa][which]=ch[x][which^1];
    f[ch[x][which^1]]=fa;
    ch[x][which^1]=fa;
    f[fa]=x;
    update(fa);
    update(x);
}
void splay(int x){
    for(int fa;fa=f[x];rotate(x))
        if(f[fa])
            rotate(chk(x)==chk(fa)?fa:x);
    root=x;
}
void insert(int x){
    if(!root){
        size++;
        root=size;
        sz[size]=1;
        cnt[size]=1;
        val[size]=x;
        return;
    }
    int now=root,fa=0;
    while(1){
        if(val[now]==x){
            cnt[now]++;
            sz[now]++;
            update(now);
            update(fa);
            splay(now);
            return;
        }
        fa=now;
        now=ch[now][x>val[now]];
        if(!now){
            size++;
            cnt[size]=sz[size]=1;
            f[size]=fa;
            val[size]=x;
            ch[size][0]=ch[size][1]=0;
            ch[fa][x>val[fa]]=size;
            update(fa);
            splay(size);
            return;
        }
    }
}
int get_pre(){
    int now=ch[root][0];
    while(ch[now][1]) now=ch[now][1];
    return now;
}
int get_nxt(){
    int now=ch[root][1];
    while(ch[now][0]) now=ch[now][0];
    return now;
}
int get_rank(int x){
    int now=root,res=0;
    while(1){
        if(x<val[now]) now=ch[now][0];
        else{
            res+=ch[now][0]?sz[ch[now][0]]:0;
            if(x==val[now]){
                splay(now);
                return res+1;
            }
            res+=cnt[now];
            now=ch[now][1];
        }
    }
}
int get_num(int x){
    int now=root;
    while(1){
        if(ch[now][0]&&x<=sz[ch[now][0]]) now=ch[now][0];
        else{
            int t=(ch[now][0]?sz[ch[now][0]]:0)+cnt[now];
            if(x<=t) return val[now];
            x-=t;
            now=ch[now][1];
        }
    }
}
void del(int x){
    get_rank(x);
    if(cnt[root]>1){
        cnt[root]--;
        sz[root]--;
        return;
    }
    if(!ch[root][0]&&!ch[root][1]){
        clear(root);
        root=0;
        return;
    }
    if(!ch[root][0]){
        int t=root;
        root=ch[root][1];
        clear(t);
        f[root]=0;
        return;
    }
    if(!ch[root][1]){
        int t=root;
        root=ch[root][0];
        clear(t);
        f[root]=0;
        return;
    }
    int pre=get_pre(),t=root;
    splay(pre);
    ch[root][1]=ch[t][1];
    f[ch[t][1]]=root;
    clear(t);
    update(root);
}
//-------------以上splay-----------------
int query(int &now,int l,int r,int k){//权值线段树查询k大 
    if(!now) now=++num;
    if(l==r) return l;
    int mid=(l+r)>>1,s=mid-l+1-sum[ls[now]];
    if(s>=k) return query(ls[now],l,mid,k);
    else return query(rs[now],mid+1,r,k-s);
}
void update(int &now,int l,int r,int x){//权值线段树删除位置x上的数 
    if(!now) now=++num;
    if(l==r){
        sum[now]=1;
        return;
    }
    int mid=(l+r)>>1;
    if(x<=mid) update(ls[now],l,mid,x);
    else update(rs[now],mid+1,r,x);
    sum[now]=sum[ls[now]]+sum[rs[now]];
}
signed main(){
    n=read(),m=read(),q=read();
    for(int i=1;i<=n;i++) insert(i),ma[i]=i*m;
    for(int i=1;i<=n;i++) leave[i]=m-1;
    last=n*m,R=n;
    while(q--){
        int x=read(),y=read();
        if(x==n&&y==m) write(last),hh;
        else if(y==m){
            int t=get_num(x);//最后一列第x个 
            write(ma[t]),hh;
            last=ma[t];
            //ma值映射编号 
            del(t);
            ma[++R]=last;
            insert(R);
        }
        else{
            int ans;
            if(y<=leave[x]){
                leave[x]--;
                ans=query(rt[x],1,m-1,y);
                int id=ans;
                ans=last=(x-1)*m+ans;
                write(ans),hh;
                update(rt[x],1,m-1,id);
            }
            else{
                write(v[x][y-leave[x]-1]),hh;
                ans=last=v[x][y-leave[x]-1];
                v[x].erase(v[x].begin()+y-leave[x]-1);
            } 
            int t=get_num(x);//最后一列第x个
            v[x].push_back(ma[t]);
            del(t);
            ma[++R]=ans;
            insert(R);
        }
    }
    return 0;
}
```