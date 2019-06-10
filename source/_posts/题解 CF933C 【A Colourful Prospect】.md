---
title: 题解 CF933C 【A Colourful Prospect】
date: 2019-06-10 19:33:22
top: 1
tag: 
- 题解
- Codeforces
- 计算几何
---
[传送门](https://www.luogu.org/problemnew/show/CF933C)

调了一下午才调出来。主要是这道题的数据太能构造了，导致每次改完都只能多过几个点。

对于这种问平面被图形分割成几个部分的题，如果你对欧拉公式熟悉的话，发现直接套用就好：
$$
V-E+R=2
$$
其中，$V$是交点个数，$E$是边数，$R$是被分割的平面数。

由于这道题几个分割的圆可以不连通，改一下公式即可：
$$
V-E+R=C+1
$$
其中$C$是平面上圆所构成的联通块个数。

发现答案就是求$R$，所以我们考虑求出$V,E,C$：

首先是求$V$，也是最麻烦的一个，其实就是圆与圆求交点，然后进行去重。$dalao$请略过。我是直接利用两个圆的解析方程相减，求出经过交点的直线，然后再直线与圆求交。~~应该有更简单的方法，我太菜了。~~ 具体在草稿纸上手算一下即可，然后存下所有的点排序去重。

$WA$了十多次，最后发现主要是求直线与圆的交点，解一元二次方程算$delta$的时候，如果两圆刚好相切，算出来总是$-0.0000000\dots$导致错误。所以用$eps$判一下

求$E$，直接统计每个圆上的交点个数，累加即可。

求$C$，对于有交的两个圆，用并查集并起来，最后就是联通块个数。

代码：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
using namespace std;
int n,R,V,E,C,tot,f[105],s[105],vis[105];
double eps=1e-5,r[105];
struct point{
    double x,y;
    int id1,id2;
}a[105],jiao[105];
//R+V-E=C+1
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
point operator + (point A,point B){
    return (point){A.x-B.x,A.y+B.y};
}
point operator - (point A,point B){
    return (point){A.x-B.x,A.y-B.y};
}
inline int dis(point A,point B){
    return (A.x-B.x)*(A.x-B.x)+(A.y-B.y)*(A.y-B.y);
}
inline int find(int p){
    return f[p]==p?p:f[p]=find(f[p]);
}
inline bool cmp(point A,point B){
    return fabs(A.x-B.x)<eps?A.y<B.y:A.x<B.x;
}
signed main(){
    n=read();
    for(int i=1;i<=n;i++){
        a[i].x=read();
        a[i].y=read();
        r[i]=read();
        f[i]=i;
    }
    C=n;
    for(int i=1;i<=n;i++){//这么长全是在求圆的交点
        for(int j=i+1;j<=n;j++){
            int ds=r[i]+r[j];
            if(ds*ds<dis(a[i],a[j])) continue;//两圆相离
            int tx=find(i),ty=find(j);
            if(dis(a[i],a[j])<fabs(r[i]-r[j])*fabs(r[i]-r[j])) continue;
            if(tx!=ty) f[tx]=ty,C--;
            double a1=a[i].x,b1=a[i].y,a2=a[j].x,b2=a[j].y,r1=r[i],r2=r[j];
            double k,b;
            if(fabs(b1-b2)<eps){
                double x=(a2*a2-a1*a1+r1*r1-r2*r2)/(2*a2-2*a1);
                jiao[++tot].x=x;
                jiao[tot].y=b1+sqrt(r1*r1-(x-a1)*(x-a1));
                jiao[tot].id1=i;
                jiao[tot].id2=j;
                jiao[++tot].x=x;
                jiao[tot].y=b1-sqrt(r1*r1-(x-a1)*(x-a1));
                jiao[tot].id1=i;
                jiao[tot].id2=j;
                if(fabs(sqrt(r1*r1-(x-a1)*(x-a1)))<eps) tot--;
                continue;
            }
            else k=(2*a1-2*a2)/(2*b2-2*b1),b=(r1*r1-r2*r2+a2*a2+b2*b2-a1*a1-b1*b1)/(2*b2-2*b1);
            // printf("k=%.2lf\n",k);
            double A=(k*k+1),B=(2*b*k-2*a1-2*k*b1),C=(a1*a1+b*b-2*b*b1+b1*b1-r1*r1);
            double delta=B*B-4*A*C;
            if(fabs(delta)<eps) delta=0;
            jiao[++tot].x=((-B)-sqrt(delta))/(2*A);
            jiao[tot].y=jiao[tot].x*k+b;
            jiao[tot].id1=i;
            jiao[tot].id2=j;
            jiao[++tot].x=((-B)+sqrt(delta))/(2*A);
            jiao[tot].y=jiao[tot].x*k+b;
            jiao[tot].id1=i;
            jiao[tot].id2=j;
            if(fabs(delta)<eps) tot--;
        }
    }
    sort(jiao+1,jiao+tot+1,cmp);
    jiao[tot+1].x=-666,jiao[tot+1].y=-666;
    for(int i=1;i<=tot;i++){
        // printf("%.2lf %.2lf id1=%d id2=%d\n",jiao[i].x,jiao[i].y,jiao[i].id1,jiao[i].id2);
        vis[jiao[i].id1]=vis[jiao[i].id2]=1;
        if(fabs(jiao[i].x-jiao[i+1].x)>eps||fabs(jiao[i].y-jiao[i+1].y)>eps){
            for(int j=1;j<=n;j++) if(vis[j]) s[j]++;
            V++;
            memset(vis,0,sizeof(vis));
            continue;
        }
    }
    for(int i=1;i<=n;i++) E+=s[i];
    // printf("%d\n",E);
    printf("%d",E+C-V+1);
    return 0;
}
```