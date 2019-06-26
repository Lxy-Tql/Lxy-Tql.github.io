---
title: 题解 luoguP3025 【[USACO11OPEN]忘记密码Forgotten Password】
date: 2019-06-26 14:33:05
top: 1
tag:
- 题解
- Luogu
- 动态规划
- 字符串
---
[传送门](https://www.luogu.org/problemnew/show/P3025)

或许你们更愿意看短一点的代码。

每个密码单词长度小于等于$20$，那么我们在$dp$时直接暴力判断能不能匹配。

$ans[i]$表示长度为$i$时候的答案，为$""$时表示不存在。对于原密码的每一位，枚举所有单词，钦定这个单词的结尾为这一位，然后找到开头，看一下中间一段能不能匹配。如果能匹配，看一下答案是否更优。判断字典序以及字符串拼接利用强大的$string$即可。

代码很短很好懂：

复杂度$O(L\times NW\times 20)$。
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
using namespace std;
int len,n;
char p[1005];
string s[1005],ans[1005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
}
inline bool check(int st,int id){
    for(int i=0;i<s[id].size();i++)
        if(p[st+i]!='?'&&p[st+i]!=s[id][i])
            return 0;
    return 1;
}
signed main(){
    len=read(),n=read();
    scanf("%s",p+1);
    for(int i=1;i<=n;i++) cin>>s[i];
    for(int i=1;i<=len;i++){
        for(int j=1;j<=n;j++){
            if(s[j].size()>i) continue;
            int st=i-s[j].size();
            if(st>0&&ans[st]=="") continue;
            if(check(st+1,j))
                if(ans[i]==""||ans[i]>ans[st]+s[j])
                    ans[i]=ans[st]+s[j];
        }
    }
    cout<<ans[len];
    return 0;
}
```