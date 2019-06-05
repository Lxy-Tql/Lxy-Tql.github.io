---
title: 题解 luoguP2928 【[USACO09HOL]牛的打手Cattle Bruisers】
date: 2019-06-05 15:15:45
top: 1
tag:
- 题解
- Luogu
- 计算几何
---
[传送门](https://www.luogu.org/problemnew/show/P2928)

没有题解，来补一篇。

观察到题目中$Bessie$和杀手都在动，所以考虑相对运动，以$Bessie$的位置作为原点，$Bessie$不动，只考虑杀手运动。让$Bessie$不动，直接杀手的位置和速度减去$Bessie$的即可。

考虑杀手攻击半径为$R$，转化一下即进入以$Bessie(0,0)$为圆心，$R$为半径的圆，就可攻击到$Bessie$。

所以我们只要求出每个杀手进入该圆与离开该圆的时间点即可。最后就可以对所以时间点进行排序，然后对整条时间线进行类似于扫描线的方法统计答案。

显然就是这样一张图：![](https://s2.ax1x.com/2019/06/05/VNvuY4.png)

求出直线与圆的交点即可。

设杀手起点为$(x,y)$，速度为$(vx,vy)$，设在$t$时刻恰好在交点，那么：
$$
(x+t\times vx)^2+(y+t\times vy)^2=r^2
$$
化简得到一个一元二次方程：
$$
(vx^2+vy^2)\times t^2+(2x\times vx+2y\times vy)\times t+x^2+y^2-r^2=0
$$
解出来即可。$($即程序里的$work)$

注意判断$vx,vy$均为0的情况。

```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define int long long
#define hh puts("")
using namespace std;
int n,r,top;
double bx,by,bvx,bvy,eps=1e-8,L,R;
struct node{
    double x,y,vx,vy;
}a[500005];
struct TM{
    double t;
    int s;
}b[500005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-1;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline void work(double A,double B,double C){//a*t*t+b*t+c=0
    if(fabs(A)<eps){
        if(C<=0) L=0,R=1e9;
        else L=R=-1;
        return;
    }
    double delta=B*B-4*A*C;
    if(delta<0){//一元二次方程判根 
        L=R=-1;
        return;
    }
    L=(-B-sqrt(delta))/(2*A);
    R=(-B+sqrt(delta))/(2*A);
    if(L<0) L=0;
    if(R<0) R=-1;
}
inline bool cmp(TM a,TM b){
    return a.t<b.t;
}
signed main(){
    n=read(),r=read();
    bx=read(),by=read(),bvx=read(),bvy=read();
    for(int i=1;i<=n;i++){
        a[i].x=read()-bx;
        a[i].y=read()-by;
        a[i].vx=read()-bvx;
        a[i].vy=read()-bvy;
    }
    for(int i=1;i<=n;i++){
        double ds=a[i].x*a[i].x+a[i].y*a[i].y-r*r;
        work(a[i].vx*a[i].vx+a[i].vy*a[i].vy,2*a[i].x*a[i].vx+2*a[i].y*a[i].vy,ds);
        if(R!=-1) b[++top]=(TM){L,1},b[++top]=(TM){R,-1};
    }
    sort(b+1,b+top+1,cmp);
    int sum=0,ans=0;
    for(int i=1;i<=top;i++){
        sum+=b[i].s;
        ans=max(ans,sum);
    }
    printf("%lld",ans);
    return 0;
}
```
