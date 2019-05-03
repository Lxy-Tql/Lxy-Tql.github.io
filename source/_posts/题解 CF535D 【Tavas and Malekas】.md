---
title: 题解 CF535D 【Tavas and Malekas】
date: 2019-05-03 15:55:07
top: 1
tag: 
- 题解
- Codeforces
- Hash
- KMP
---
[传送门](https://www.luogu.org/problemnew/show/CF535D)

首先很容易发现答案就是$26​$的“空位个数”次方。空位的个数很显然能够通过~~极其方便的~~差分处理出来，我们要处理的仅仅只有判断是否无解。

分两种情况：

第一种，两个串不相交，显然什么关系都没有。

第二种，两个串相交，我们要判断相交部分是不是相同。发现相交的是第一个字符串的后缀，和第二个字符串的前缀，判断这两段是否相同，那么最容易想到的就是$Hash$了。$Hash$在$cf$上极其危险，有人会对着你卡。所以尽量写个双$Hash$即可。

观察到要判断前缀和后缀的关系，那么显然$KMP$也是可以的。$KMP$的$next$数组就是判断以每一位结尾的字符串前缀和后缀有多少位相等的。那么我们就可以用$KMP$算出，哪些长度的重叠部分是满足条件的。举个栗子，串$abababab$，$next[8]=6$，那么重叠部分长度为$6$就是符合条件的，然后再算$next[6]=4$，$next[4]=2$ $\cdots$，所以重叠部分长度为$4,2$都是符合条件的。

代码：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
#define mo 1000000007
using namespace std;
int n,m,p[1000005],len,a[1000005],sum[1000005],vis[1000005];
char s[1000005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline ll ksm(ll x,ll y){
    ll res=1;
    while(y){
        if(y&1) res=res*x%mo;
        y>>=1;
        x=x*x%mo;
    }
    return res;
}
signed main(){
    n=read(),m=read();
    scanf("%s",s+1);
    len=strlen(s+1);
    int j=0;
    p[1]=0;
    for(int i=2;i<=len;i++){//kmp
        while(j>0&&s[i]!=s[j+1]) j=p[j];
        if(s[i]==s[j+1]) j++;
        p[i]=j;
    }
    for(int i=len;i;i=p[i]) vis[i]=1;//哪些长度的重叠符合条件
    for(int i=1;i<=m;i++) a[i]=read(),sum[a[i]]++,sum[a[i]+len]--;
    for(int i=2;i<=m;i++){
        if(a[i]-a[i-1]<len){
            if(!vis[a[i-1]+len-a[i]]){//a[i-1]+len-a[i]即重叠长度，很容易手玩得出
                printf("0");
                return 0;
            }
        }
    }
    int cnt=0,s=0;
    for(int i=1;i<=n;i++){
        s+=sum[i];
        if(s==0) cnt++;
    }
    printf("%lld",ksm(26,cnt));
    return 0;
}
```