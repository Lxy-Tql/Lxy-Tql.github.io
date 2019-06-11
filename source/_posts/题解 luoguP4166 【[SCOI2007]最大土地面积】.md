---
title: 题解 luoguP4166 【[SCOI2007]最大土地面积】
date: 2019-06-12 07:41:26
top: 1
tag:
- 题解
- Luogu
- 计算几何
- 凸包
- 三分
---
[传送门](https://www.luogu.org/problemnew/show/P4166)

这里补充一下三分的做法。

首先$n^2$枚举对角线，然后我们要算的是在对角线左边最大的三角形和对角线右边最大的三角形。

显然如果我们任选一侧，从对角线的一个顶点到另一个顶点依次计算，三角形的面积肯定是先增大后减小的，所以考虑三分。

显然我们三分凸包上点的下标，然而我们会遇到一个问题：比如我们的顶点标号是$0-6$，当前我们枚举到对角线$1-4$，然后我们在对角线左边三分的话，我们发现这些点的下标变成了$5,6,0$，三分起来很麻烦。

这里我用的小技巧，就是把点再复制一份，即总共有$cnt$个点，我们分成$0- (cnt-1)$，$cnt-(2\times cnt-1)$相同的两份，枚举对角线时，我们在第二份中枚举，比如枚举到$(i,j)$，那么三分时，左右区间分别是$(j-cnt+1,i-1)$，$(i+1,j-1)$，实现起来就比较方便。

注意三分时，因为我们三分参数是下标，所以当$r-l=2$时特判一下，否则会死循环。

总复杂度$O(n^2 log\ n)$

```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
using namespace std;
int n,st[10005],top,cnt;
double eps=1e-8,ans;
struct point{
    double x,y;
}a[10005],t[10005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline bool cmp(point A,point B){
    return fabs(A.x-B.x)<eps?A.y<B.y:A.x<B.x;
}
inline double cross(point A,point B){
    return A.x*B.y-A.y*B.x;
}
point operator - (point A,point B){
    return (point){A.x-B.x,A.y-B.y};
}
signed main(){
    n=read();
    for(int i=1;i<=n;i++) scanf("%lf%lf",&a[i].x,&a[i].y);
    sort(a+1,a+n+1,cmp);
    top=0;
    for(int i=1;i<=n;i++){
        st[++top]=i;
        while(top>=3&&cross(a[st[top]]-a[st[top-2]],a[st[top-1]]-a[st[top-2]])>=0){
            st[top-1]=st[top];
            top--;
        }
    }
    for(int i=1;i<=top;i++) t[++cnt]=a[st[i]];
    top=0;
    for(int i=1;i<=n;i++){
        st[++top]=i;
        while(top>=3&&cross(a[st[top]]-a[st[top-2]],a[st[top-1]]-a[st[top-2]])<=0){
            st[top-1]=st[top];
            top--;
        }
    }
    for(int i=top-1;i>=2;i--) t[++cnt]=a[st[i]];
    t[0]=t[cnt];
    for(int i=cnt+1;i<2*cnt;i++) t[i]=t[i-cnt];
    for(int i=cnt;i<2*cnt;i++){
        for(int j=i+2;j<2*cnt;j++){
            int l,r,lmid,rmid,left,right;
            l=i+1,r=j-1;
            point D=t[j]-t[i];
            while(r-l>1){
                if(r-l==2){
                    if(cross(t[l]-t[i],D)>cross(t[r]-t[i],D)) r--;
                    else l++;
                    break;
                }
                lmid=l+(r-l)/3;
                rmid=r-(r-l)/3;
                if(cross(t[lmid]-t[i],D)>cross(t[rmid]-t[i],D)) r=rmid;
                else l=lmid;
            }
            if(r-l==1){
                if(cross(t[r]-t[i],D)>cross(t[l]-t[i],D)) right=r;
                else right=l;
            }
            else right=l;
            r=i-1,l=j-cnt+1;
            while(r-l>1){
                if(r-l==2){
                    if(cross(D,t[l]-t[i])>cross(D,t[r]-t[i])) r--;
                    else l++;
                    break;
                }
                lmid=l+(r-l)/3;
                rmid=r-(r-l)/3;
                if(cross(D,t[lmid]-t[i])>cross(D,t[rmid]-t[i])) r=rmid;
                else l=lmid;
            }
            if(r-l==1){
                if(cross(D,t[r]-t[i])>cross(D,t[l]-t[i])) left=r;
                else left=l;
            }
            else left=l;
            ans=max(ans,(cross(t[right]-t[i],D)+cross(D,t[left]-t[i]))/2.);
        }
    }
    printf("%.3lf",ans);
    return 0;
}
```