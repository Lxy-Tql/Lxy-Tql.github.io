---
title: 题解 CF1203F2 【Complete the Projects (hard version)】
date: 2019-08-18 09:40:14
top: 1
tag: 
- 题解
- Codeforces
- 动态规划
- 贪心
---
[传送门](https://www.luogu.org/problem/CF1203F2)

对于$b_i$为正的任务，显然贪心，按$a_i$从小到大排序，当前能力值大于等于$a_i$就选上。

剩下一堆任务，题目变成，选第$i$个任务需要$a_i$能力值，做完这个任务能力值减少$b_i$。考虑$dp$，$f[i][j]$表示做前$i$个任务，能力值为$j$，最多能做多少个任务。

枚举前面的任务，枚举能力值，满足条件则转移。

$dp$前要对任务排序，我们考虑按$a_i+b_i$排序，这样显然后面的任务不会影响前面的任务。

$Code \ Below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
#define pc putchar
//#define getchar() (p1==p2&&(p2=(p1=buf)+fread(buf,1,1<<21,stdin),p1==p2)?EOF:*p1++)
//char buf[1<<21],*p1=buf,*p2=buf;
using namespace std;
int n,r,cnt1,cnt2,ans,f[105][60005],res;
struct node{
    int x,y;
}a[10005],b[10005];
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
inline bool cmp1(node A,node B){
    return A.x<B.x;
}
inline bool cmp2(node A,node B){
    return A.x+A.y>B.x+B.y;
}
signed main(){
    n=read(),r=read();
    for(int i=1;i<=n;i++){
        int x=read(),y=read();
        if(y>=0) a[++cnt1]=(node){x,y};
        else b[++cnt2]=(node){x,y};
    }
    sort(a+1,a+cnt1+1,cmp1);
    int now=r;
    for(int i=1;i<=cnt1;i++){//先把正的都选上 
        if(now>=a[i].x){
            ans++;
            now+=a[i].y;
        }
        else break;
    }
    sort(b+1,b+cnt2+1,cmp2);
    for(int i=1;i<=cnt2;i++){
        if(now>=b[i].x&&now>=abs(b[i].y)){
            f[i][now-abs(b[i].y)]=1;
            res=max(res,1);
            for(int j=i-1;j>=1;j--)
                for(int k=max(abs(b[i].y),b[i].x);k<=60000;k++)
                    if(f[j][k]){
                        f[i][k-abs(b[i].y)]=max(f[i][k-abs(b[i].y)],f[j][k]+1);    
                        res=max(res,f[i][k-abs(b[i].y)]);
                    }
        }
    }
    write(ans+res);
    return 0;
}
```