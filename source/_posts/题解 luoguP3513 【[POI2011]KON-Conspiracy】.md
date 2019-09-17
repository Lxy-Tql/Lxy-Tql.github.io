---
title: 题解 luoguP3513 【[POI2011]KON-Conspiracy】
date: 2019-09-17 19:52:38
top: 1
tag: 
- 题解
- Luogu
- tarjan
- 2-sat
---
[传送门](https://www.luogu.org/problem/P3513)

题目大意就是，给出$n$个点，分成一个团和一个独立集的方案数。

刚看到题没什么思路，稍微想一想，我们可以得到一个初步的结论。

我们发现，如果我们找到了一组合法的方案，那么我们从团中移出两个点到独立集中是不可行的，因为这两个点必定有边。同理，从独立集移两个点到团中也是不可行的，因为这两个点之间必定没有边。

由此我们发现，对于一个合法的方案，所以如果有另外合法的方案，必定只能在这个方案的基础上改变一个点$($把一个点从独立集移到团中,或把一个点从团移到独立集中，或交换独立集和团中的各一个点$)$。

那么我们考虑怎么找合法的方案？

显然的$2-SAT$问题，每个点拆两个点,表示分到团里还是团外，$i($编号是$i\times 2-1)$表示团里，$i'($编号是$i\times 2)$表示团外。

若$i,j$有边，$i'->j,j'->i$；若没边，$i->j',j->i'$。$($箭头表示连边$)$。

然后我们利用拓扑序就能找到一组合法解。$($不清楚的话可参考$2-SAT$模板$)$

剩下就是求之前所说的改变一个点可以到达的方案。$n^2$统计一下即可。~~只是一个小模拟~~ 还有，$yy$一下发现$2$要特判。

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
const int N=5005;
int n,dfn[N<<1],low[N<<1],st[N<<1],top,ans;
int col[N<<1],dep,vis[N<<1],p[N],col_cnt,nei[N],c1,wai[N],c2,a[N],b[N];
bool f[N][N];
vector<int> g[N<<1];
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
    g[x].push_back(y);
}
void tarjan(int now){
    dfn[now]=low[now]=++dep;
    st[++top]=now;
    vis[now]=1;
    for(int i=0;i<(int)g[now].size();i++){
        int v=g[now][i];
        if(!dfn[v]){
            tarjan(v);
            low[now]=min(low[now],low[v]);
        }
        else if(vis[v]) low[now]=min(low[now],low[v]);
    }
    if(dfn[now]==low[now]){
        col_cnt++;
        int t;
        do{
            t=st[top];
            top--;
            vis[t]=0;
            col[t]=col_cnt;
        }while(t!=now);
    }
}
signed main(){
    n=read();
    for(int i=1;i<=n;i++){
        int x=read();
        while(x--){
            int t=read();
            f[i][t]=1;
        }
        for(int j=1;j<=n;j++){
            if(i^j){
                if(f[i][j]) add(i*2,j*2-1);
                else add(i*2-1,j*2);
            }
        }
    }
    if(n==2){
        write(2);
        return 0;
    }
    for(int i=1;i<=2*n;i++)
        if(!dfn[i])
            tarjan(i);
    for(int i=1;i<=n;i++){
        if(col[i*2-1]==col[i*2]){
            write(0);
            return 0;
        }
    }
    for(int i=1;i<=n;i++) p[i]=col[i*2-1]>col[i*2];
    //col[i]>col[j]说明i拓扑序小于j,则取j 
    //所以p[i]=0则在团内 
//    for(int i=1;i<=n;i++) write(p[i]),pc(' ');
    for(int i=1;i<=n;i++){
        if(!p[i]) nei[++c1]=i;
        else wai[++c2]=i;
    }
    int ans=1;
    for(int i=1;i<=c1;i++){
        int x=nei[i],cnt=0;
        for(int j=1;j<=c2;j++){
            int y=wai[j];
            if(f[x][y]){
                if(cnt==1){
                    a[i]=1e9;//不能动
                    break;
                }
                cnt++;
                a[i]=y;
            }
        }
    }
    for(int i=1;i<=c2;i++){
        int x=wai[i],cnt=0;
        for(int j=1;j<=c1;j++){
            int y=nei[j];
            if(!f[x][y]){
                if(cnt==1){
                    b[i]=1e9;
                    break;
                }
                cnt++;
                b[i]=y;
            }
        }
    }
    if(c1!=1){
        for(int i=1;i<=c1;i++)
        if(!a[i])
            ans++;//把i换出去
    }
    if(c2!=1){
        for(int i=1;i<=c2;i++)
        if(!b[i])
            ans++;//把i换进来 
    }
    for(int i=1;i<=c1;i++)
        for(int j=1;j<=c2;j++)
            if((a[i]==wai[j]||!a[i])&&((b[j]==nei[i]||!b[j])))
                ans++;
    write(ans);
    return 0;
}
```