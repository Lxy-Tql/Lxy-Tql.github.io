---
title: 题解 luoguP4060 【[Code+1]可做题】
date: 2019-10-07 20:32:29
top: 1
tag: 
- 题解
- Luogu
- 贪心
- 前缀和
- 构造
---
[传送门](https://www.luogu.org/problem/P4060)

显然是让未填的一段区间前缀异或和除端点外均为$0$最优。 

我们来仔细考虑一下端点的问题：

对于一段连续的全部知道的区间，我们可以通过调节这个区间左边第一个空来使得这个区间的贡献最小，这个空位的权值我们可以枚举二进制下每一位算出来。$($注意特判从$1$开始的区间$)$。

计算过了这段区间，然后我们可以马上在它右端点右侧放一个值，消除这一段的贡献，使得之后的空位异或前缀和均为$0$。

感觉有一个小小的问题，就是两段全部知道的区间之间如果只有一个空位，是不是会出问题？

仔细考虑一下，发现是不会的。每次计算一段连续的全部知道的区间，我们算出了这段区间左边第一个空的最优填法，然后再异或上消除左边一段连续的全部知道的区间应该填的值，就是这单个空的权值了。显然仍然是最优的。

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
const int N=100005;
int n,m,ans;
struct node{
    int p,v;
    friend bool operator < (node A,node B){
        return A.p<B.p;
    }
}a[N];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
void write(int x){if(x<0){x=-x,pc('-');}if(x>9) write(x/10);pc(x%10+48);}
void writeln(int x){write(x),hh;}
void writesp(int x){write(x),pc(' ');}
int solve(int l,int r){
    int res=0,now=0;
    if(a[l].p==1){//左边不能放 
        for(int i=l;i<=r;i++){
            now^=a[i].v;
            res+=now;
        }
    }
    else{
        for(int i=30;i>=0;i--){
            int cnt[2],tot[2];
            cnt[0]=tot[0]=0;
            cnt[1]=tot[1]=1;
            for(int j=0;j<=1;j++){
                for(int k=l;k<=r;k++){
                    tot[j]^=(a[k].v>>i)&1;
                    cnt[j]+=tot[j];
                }
            }
            res+=(1<<i)*min(cnt[0],cnt[1]);
        }
    }
    return res;
}
signed main(){
    n=read(),m=read();
    for(int i=1;i<=m;i++){
        a[i].p=read();
        a[i].v=read();
    }
    sort(a+1,a+m+1);
    int now=1;
    while(now<=m){
        int last=now;
        while(now<=m&&a[now].p+1==a[now+1].p) now++;
        ans+=solve(last,now);
        now++;
    }
    write(ans);
    return 0;
}
```