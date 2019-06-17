---
title: 题解 luoguP3963 【[TJOI2013]奖学金】
date: 2019-06-18 07:35:38
top: 1
tag: 
- 题解
- Luogu
- 堆
- 贪心
- 枚举
---
[传送门](https://www.luogu.org/problemnew/show/P3963)

先成绩从大到小排序，然后考虑枚举哪一同学的成绩为中位数。

$f[i]$表示第$i$个同学的成绩作为中位数时，左边$n/2$个最小值的和

$g[i]$表示...同理，为右边$n/2$个最小值的和

$f[i],g[i]$满足$n/2+1<=i<=c-n/2($左右都必须有$n/2$个数$)$

然后从大到小枚举答案，注意$f[i],g[i]$是不包括第$i$个的，所以若$f[i]+g[i]+money[i]<=F$，输出答案。

$Code\ below:$
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
using namespace std;
int n,c,tot,f[200005],g[200005];
struct node{
    int sc,mn;
}a[200005];
priority_queue<int> q;
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
inline bool cmp(node A,node B){
    return A.sc<B.sc;
}
signed main(){
    n=read(),c=read(),tot=read();
    for(int i=1;i<=c;i++){
        a[i].sc=read();
        a[i].mn=read();
    }
    sort(a+1,a+c+1,cmp);
    int sum=0;
    for(int i=1;i<=n/2;i++){
        q.push(a[i].mn);
        sum+=a[i].mn;
    }
    for(int i=n/2+1;i<=c-n/2;i++){
        f[i]=sum;
        if(a[i].mn<q.top()){
            sum-=q.top();
            sum+=a[i].mn;
            q.pop();
            q.push(a[i].mn);
        }
    }
    while(!q.empty()) q.pop();
    sum=0;
    for(int i=c;i>=c-n/2+1;i--){
        q.push(a[i].mn);
        sum+=a[i].mn;
    }
    for(int i=c-n/2;i>=n/2+1;i--){
        g[i]=sum;
        if(a[i].mn<q.top()){
            sum-=q.top();
            sum+=a[i].mn;
            q.pop();
            q.push(a[i].mn);
        }
    }
    for(int i=c-n/2;i>=n/2+1;i--){
        if(f[i]+g[i]+a[i].mn<=tot){
            printf("%d",a[i].sc);
            return 0;
        }
    }
    printf("-1");
    return 0;
}
```