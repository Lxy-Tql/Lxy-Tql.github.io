---
title: 题解 UVA109 【SCUD Busters】
date: 2019-06-09 20:58:38
top: 1
tag: 
- 题解
- UVA
- 计算几何
- 凸包
---
[传送门](https://www.luogu.org/problemnew/show/UVA109)

题目大意：

给你一堆城市，每个城市由一堆点构成，城市的围墙是包含这个城市所有点的最小多边形，城市之间两两不相交。然后有给出一堆导弹的坐标，若一个导弹打在某个城市内部即可摧毁这个城市，问导弹摧毁的城市的总面积。

整道题显然就是考了对计算几何模板的掌握，显然先求出每个城市的凸包，然后判断导弹是否在凸包内。

对于判断一个点是否在凸包内，可以$O(log \ n)$求$(n$是凸包上点的个数$)$。

我们在求凸包时把点按逆时针顺序存起来，然后对于一个点，我们可以根据斜率二分出它在凸包的哪两个向量之间，那么问题就变成了点是否在三角形内。利用叉积解决即可。见代码中的$work$函数。

注意一个城市只能被摧毁一次。

代码：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
using namespace std;
int n,tot,sz[25],st[1005],top,vis[25];
double S[25],ans;
struct point{
    int x,y;
}a[25][105],t[105],low[25];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline bool cmp(point A,point B){
    return A.x==B.x?A.y<B.y:A.x<B.x;
}
point operator + (point A,point B){
    return (point){A.x+B.x,A.y+B.y};
}
point operator - (point A,point B){
    return (point){A.x-B.x,A.y-B.y};
}
inline int cross(point A,point B){
    return A.x*B.y-A.y*B.x;
}
inline void Graham(int id){//求凸包
    sort(a[id]+1,a[id]+sz[id]+1,cmp);
    int cnt=0;
    top=0;
    for(int i=1;i<=sz[id];i++){
        st[++top]=i;
        while(top>=3&&cross(a[id][st[top]]-a[id][st[top-2]],a[id][st[top-1]]-a[id][st[top-2]])>=0){
            st[top-1]=st[top];
            top--;
        }
    }
    for(int i=1;i<=top;i++) t[++cnt]=a[id][st[i]];
    top=0;
    for(int i=1;i<=sz[id];i++){
        st[++top]=i;
        while(top>=3&&cross(a[id][st[top]]-a[id][st[top-2]],a[id][st[top-1]]-a[id][st[top-2]])<=0){
            st[top-1]=st[top];
            top--;
        }
    }
    for(int i=top-1;i>=2;i--) t[++cnt]=a[id][st[i]];
    
    sz[id]=cnt;
    for(int i=1;i<=cnt;i++) a[id][i]=t[i];
    low[id]=a[id][1];
    for(int i=1;i<=cnt;i++) a[id][i]=a[id][i]-low[id];
    if(sz[id]<=2) return;
    for(int i=1;i<cnt;i++) S[id]+=abs(cross(a[id][i],a[id][i+1]));//计算凸包面积
    S[id]+=cross(a[id][cnt],a[id][1]);
    S[id]/=2.;
}
inline int len(point A){
    return A.x*A.x+A.y*A.y;
}
inline bool comp(point A,point B){
    return cross(A,B)>0||(cross(A,B)==0&&len(A)<len(B));
}
inline bool check(point A,point B,point C){
    if(abs(cross(A,C))+abs(cross(C,B))+abs(cross(A-C,B-C))==abs(cross(A,B))) return 1;
    return 0;
}
inline bool work(point A,int id){//判断点A是否在第id个凸包内
    if(cross(a[id][sz[id]],A)>0) return 0;
    int pos=lower_bound(a[id]+1,a[id]+sz[id]+1,A,comp)-a[id];
    return check(a[id][pos-1],a[id][pos],A);
}
signed main(){
    while(n=read(),n!=-1){
        tot++;
        sz[tot]=n;
        for(int i=1;i<=sz[tot];i++){
            a[tot][i].x=read();
            a[tot][i].y=read();
        }
    }
    for(int i=1;i<=tot;i++) Graham(i);
    point Q;
    while(scanf("%d%d",&Q.x,&Q.y)!=EOF){
        for(int i=1;i<=tot;i++){
            if(vis[i]) continue;
            if(work(Q-low[i],i)){
                vis[i]=1;
                ans+=S[i];
                break;
            }
        }
    }
    printf("%.2lf\n",ans);
    return 0;
}
```