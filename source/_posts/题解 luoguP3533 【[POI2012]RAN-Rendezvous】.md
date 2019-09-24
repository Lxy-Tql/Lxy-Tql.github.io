---
title: 题解 luoguP3533 【[POI2012]RAN-Rendezvous】
date: 2019-09-24 13:13:09
top: 1
tag: 
- 题解
- Luogu
- 基环树
- 倍增
- LCA
---
[传送门](https://www.luogu.org/problem/P3533)

感觉我写的最麻烦。

发现是基环树森林，显然先并查集一波，不在同一集内输出$(-1\ -1)$。

否则必然有解，然后发现一个点最终肯定走到环上，把环搞出来。

处理出每个点首次走到环的那个点是哪个，为$to[i]$。

然后如果两个点$to[i]$相同，那么这两个点的答案就是这两个点的$lca$。

否则肯定要先走到环上，然后肯定是其中一个点走向另一个点，算一下就好了。

由于本人菜，代码实现较为...复杂。

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
const int N=500005;
int n,q,fa[N],f[N],bz[N][20],vis[N],col[N],id[N],ans1,ans2;
int col_cnt,size[N],inst[N],st[N],to[N],step[N],d[N],head[N],cnt;
struct Edge{
    int v,nx;
}e[N<<1];
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
void writeln(int x){write(x),hh;}
void add(int x,int y){
    e[++cnt].v=y;
    e[cnt].nx=head[x];
    head[x]=cnt;
}
int find(int p){
    return f[p]==p?p:f[p]=find(f[p]);
}
void uni(int x,int y){
    int tx=find(x),ty=find(y);
    if(tx!=ty) f[tx]=ty;
}
void dfs(int now,int fa){
    d[now]=d[fa]+1;
    bz[now][0]=fa;
    for(int i=head[now];i;i=e[i].nx){
        int v=e[i].v;
        if(v==fa) continue;
        dfs(v,now);
    }
}
void lca(int x,int y){
    if(d[x]>d[y]){
        for(int i=19;i>=0;i--){
            if(d[bz[x][i]]>=d[y]){
                x=bz[x][i];
                ans1+=1<<i;
            }
        }
    }
    else if(d[y]>d[x]){
        for(int i=19;i>=0;i--){
            if(d[bz[y][i]]>=d[x]){
                y=bz[y][i];
                ans2+=1<<i;
            }
        }
    }
    if(x==y) return;
    for(int i=19;i>=0;i--){
        if(bz[x][i]!=bz[y][i]){
            x=bz[x][i],y=bz[y][i];
            ans1+=1<<i,ans2+=1<<i;
        }
    }
    ans1++,ans2++;
}
int dis(int x,int y){//环上两点距离 
    return (id[x]-id[y]+size[col[x]])%size[col[x]];
}
signed main(){
    n=read(),q=read();
    for(int i=1;i<=n;i++) f[i]=i;
    for(int i=1;i<=n;i++){
        fa[i]=read();
        uni(fa[i],i);
        bz[i][0]=fa[i];
    }
    for(int i=1;i<=n;i++){//求环,这个自己想怎么写怎么写 
        if(!vis[i]){
            int now=i;
            int top=0;
            while(!vis[now]){
                st[++top]=now;
                inst[now]=1;
                vis[now]=1;
                now=fa[now];
                if(inst[now]){
                    col_cnt++;
                    int t,idd=0;
                    do{
                        t=st[top];
                        top--;
                        inst[t]=0;
                        col[t]=col_cnt;
                        size[col_cnt]++;
                        id[t]=++idd;
                    }while(t!=now);
                }
            }
            while(top){
                inst[st[top]]=0;
                top--;
            }
        }
    }
    for(int i=1;i<=n;i++){
        if(col[i]) continue;
        add(fa[i],i);
    }
    for(int i=1;i<=n;i++)//环上每个点的子树预处理 
        if(col[i])
            dfs(i,0);
    for(int k=1;k<=19;k++)
        for(int i=1;i<=n;i++)
            bz[i][k]=bz[bz[i][k-1]][k-1];
    for(int i=1;i<=n;i++){//预处理每个点走到环上是哪个点,并且要走几步 
        if(col[i]) to[i]=i;
        else{
            int now=i;
            for(int k=19;k>=0;k--)
                if(bz[now][k]){
                    now=bz[now][k];
                    step[i]+=1<<k;
                }
            to[i]=now;
        }
    }
    while(q--){
        int x=read(),y=read();
        if(x==y){
            puts("0 0");
            continue;
        }
        int tx=find(x),ty=find(y);
        if(tx!=ty){
            puts("-1 -1");
            continue;
        }
        tx=to[x],ty=to[y];
        if(tx==ty){
            ans1=0,ans2=0;
            lca(x,y);
            write(ans1),pc(' '),writeln(ans2);
        }
        else{//按规定输出答案 
            ans1=step[x],ans2=step[y];
            int t1=max(ans1+dis(tx,ty),ans2),t2=max(ans1,ans2+dis(ty,tx));
            if(t1<t2){
                write(ans1+dis(tx,ty)),pc(' '),writeln(ans2);
            }
            else if(t1>t2){
                write(ans1),pc(' '),writeln(ans2+dis(ty,tx));
            }
            else{
                t1=min(ans1+dis(tx,ty),ans2),t2=min(ans1,ans2+dis(ty,tx));
                if(t1<t2){
                    write(ans1+dis(tx,ty)),pc(' '),writeln(ans2);
                }
                else if(t1>t2){
                    write(ans1),pc(' '),writeln(ans2+dis(ty,tx));
                }
                else{
                    write(ans1+dis(tx,ty)),pc(' '),writeln(ans2);
                }
            }
        }
    }
    return 0;
}
```