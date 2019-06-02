---
title: 题解 luoguP2322 【[HNOI2006]最短母串问题】
date: 2019-05-06 07:33:05
top: 1
tag:
- 题解
- Luogu
- 状压dp
- 动态规划
- 字符串
---
[传送门](<https://www.luogu.org/problemnew/show/P2322>)

看到$n<=12$的数据范围，一眼状压$dp​$？随便开始敲了敲，一晚上就没了？

看到题解还有一篇状压，感觉挺麻烦的。这里给出可能稍微方便一点的。

状压状态和方程应该很好想，$f[i][j]$表示状态$i$下，最后的字符串是$j$的最短长度，因为要输出方案，再来一个$g[i][j]$表示长度最短时的字符串。

做过几题字符串接龙状压题，应该很容易写出转移方程：
$$
f[to][k]=min(f[to][k],f[i][j]+chongdie)
$$
$to$显然就是$i\ |\ (1<<(k-1))$，即状态中多接了一个$k$，$chongdie$是$k$接在$j$后面时两个字符串的重叠部分的长度，注意是有顺序的，是$k$接$j$后面。最后，当$f$数组更新时，我们同时也更新$g$数组。

具体分析一下转移：

1、$k$是$j$的子串，可以用$string$的$find$函数轻松解决，$chongdie=0$。

2、$k$不是$j$的子串，我们暴力匹配，枚举$j$从哪一位开始与$k$重叠，非常暴力，可以见代码。

答案显然易见，就是$min(\sum\limits_{i=1}^{n}f[(1<<n)-1][i]))$，然后取出所有长度最小的$g[(1<<n)-1][i]$，排遍序得到最小字典序的答案。

代码里还有部分注释，挺好懂的：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
using namespace std;
int n,f[5005][13],max_status,top;
string s[105],g[5005][13],ans[105];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=(ret<<3)+(ret<<1)+(ch^48);ch=getchar();}
    return ret*ff;
}
inline int check(int x,int y){
    if(s[x].find(s[y])!=s[x].npos) return -1;//x包含y 
    for(int i=0;i<s[x].size();i++){//枚举j从哪一位开始与k重叠
        bool fl=1;
        for(int j=i;j<s[x].size();j++){
            if(s[y][j-i]!=s[x][j]){
                fl=0;
                break;
            }
        }
        if(fl) return s[x].size()-i;//返回重叠部分长 
    }
    return 0;
}
signed main(){
    n=read();
    for(int i=1;i<=n;i++) cin>>s[i];
    memset(f,0x3f,sizeof(f));
    f[0][0]=0;
    for(int i=1;i<=n;i++) f[1<<(i-1)][i]=s[i].length(),g[1<<(i-1)][i]=s[i];
    max_status=(1<<n)-1;
    for(int i=0;i<=max_status;i++){
        for(int j=1;j<=n;j++){//以j结尾 
            if(i&(1<<(j-1))){
                for(int k=1;k<=n;k++){//后面接k 
                    if(!(i&(1<<(k-1)))){
                        int to=i|(1<<(k-1));
                        int chong=check(j,k);//重叠部分长度 
                        if(chong==-1){//j包含k 
                            if(f[i][j]<f[to][j]||(f[i][j]==f[to][j]&&g[to][j]>g[i][j])){
                                f[to][j]=f[i][j];
                                g[to][j]=g[i][j];
                            }
                        }
                        else{
                            string t="";
                            for(int p=chong;p<s[k].size();p++) t+=s[k][p];//t是j接上k后多出来的一段字符 
                            t=g[i][j]+t;
                            if((f[i][j]+s[k].size()-chong<f[to][k])||
                            (f[i][j]+s[k].size()-chong==f[to][k]&&g[to][k]>t)){
                                f[to][k]=f[i][j]+s[k].size()-chong;
                                g[to][k]=t;
                            }
                        }
                    }
                }
            }
        }
    }
    int minn=1e9;
    for(int i=1;i<=n;i++){
        if(f[max_status][i]<minn){
            minn=f[max_status][i];
            top=1;
            ans[top]=g[max_status][i];
        }
        else if(f[max_status][i]==minn) ans[++top]=g[max_status][i];
    }
    sort(ans+1,ans+top+1);//排序找字典序最小 
    cout<<ans[1];
    return 0;
}
```