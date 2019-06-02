---
title: 题解 luoguP3480 【[POI2009]KAM-Pebbles】
date: 2019-05-21 08:34:55
top: 1
tag: 
- 题解
- Luogu
- 博弈论
---
[传送门](https://www.luogu.org/problemnew/show/P3480)

~~博弈论题题都是神仙~~

这题怎么考虑？我们发现石子时刻满足从左到右递增，感觉没什么思路，那么根据博弈论的套路，我们开始。。~~天马行空地乱想~~。

先考虑$SG$函数，发现这题取石子的限制是时刻在变的，显然不行。

然后，这题多堆石子且并不相互独立，没什么表打，打表也滚粗了。

石子个数递增？？是不是长得像阶梯？？(~~大雾~~

顺着这个瞎想的思路，再康一康题目，发现移走一堆石子，它两边石子与它的差值会变。

怎么变呢？设一堆石子移走$x$，显然左边与它的差值$-x$，右边与它的差值$+x$，这不就是阶梯$nim$吗？？

什么？你不知道阶梯$nim$？那你肯定没有好好看[洛咕日报](https://www.luogu.org/blog/Wolfycz/qian-tan-suan-fa-bo-yi-lun-zong-ling-kai-shi-di-bo-yi-lun-post)。这里不详细展开了。

左边与它的差值$-x$，右边与它的差值$+x$，那么显然模型就是一个反向$($从右向左$)$的阶梯，做一遍阶梯$nim$，问题解决。

博弈论代码真的短：
```cpp
#include<bits/stdc++.h>
#define ts cout<<"ok"<<endl
#define ll long long
#define hh puts("")
using namespace std;
int n,a[1005];
inline int read(){
    int ret=0,ff=1;char ch=getchar();
    while(!isdigit(ch)){if(ch=='-') ff=-ff;ch=getchar();}
    while(isdigit(ch)){ret=ret*10+(ch^48);ch=getchar();}
    return ret*ff;
} 
signed main(){
    int T=read();
    while(T--){
        n=read();
        int ans=0;
        for(int i=1;i<=n;i++) a[i]=read();
        for(int i=n;i>=1;i--)
            if((n-i+1)&1)
                ans^=(a[i]-a[i-1]);
        puts(ans?"TAK":"NIE");
    }
    return 0;
}
```