---
title: anticube
mathjax: true
index_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.9/2233/a1.jpg
banner_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.9/2233/a1.jpg
tags: [数论,分解质因数]
categories: 数学
---



### 题意

[Link](https://atcoder.jp/contests/agc003/tasks/agc003_d)

给你 $n$ 个数，让你在这些数中间选出尽可能多的数使得，对于任意两个选出来的数，乘积不是完全立方数。



### 题解

我们可以发现，如果 $a\times b$ 为完全立方数，那么分解质因数。

$a=\prod p_i^{a_i}$,$b=\prod p_i^{b_i}$。那么应该有 $\forall i,(a_i+b_i)\bmod{3}=0$。

我们把每个数的立方因子都消掉。

$A=\prod p_i^{a_i\bmod 3}$。

所以我们只需要找出对应 $A_i+B_i=3$ 的这样一组的 $A,B$ 看那一组数多( $A,B$ 指的是消去立方因子后的)。

这样的任意一个 $A$ 对会一一对应一个使得 $A\times B=k^3$ 的 $B$。

我们只需要贪心的选择个数多的那个数即可。

---

如何快速分解质因数？

首先第一步消去立方因子的过程我们只需要枚举到 $\sqrt[3]{n}$ 的质数就行了，大于 $\sqrt[3]{n}$ 的数不可能是。

第二步对一个 $A$ 找 $B$ 的过程类似，首先暴力的找 $\sqrt[3]{lim}$ 以下范围的（注意这个是 $lim$ ，而不是你具体分解的 $n$）。

剩下的形式若不是$1$ 只可能为  $p,pq,p^2(p,q\in prime)$ 三种形式（想一下二次二项式可能性只有这仨）

只用判断一下剩下的是不是完全平方数即可。

参考代码

``` cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N=3000;
int p[N],pr[N],tot,pw[N];
map<int,int> cnt;
////////////////////////// 文化课期间重写的，看起来丑很正常
inline int div(int x)// 消去平方因子
{   
    int X=x,tx=x;
    for(int i=1;i<=tot&&pw[i]<=x;i++)
        if(x%p[i]==0) while(x%pw[i]==0) x/=pw[i];
    return x;
}
const int inf=1e10+1;
inline int sqt(int x)// 判断是否为完全平方数
{
    int v=round(sqrt(x));
    for(int i=v-2;i<=v+2;i++) if(i*i==x) return i;
    return -1;
}
inline int rebuild(int x)//A 找 B
{   
    int X=x,tx=x,res=1;
    for(int i=1;i<=tot&&p[i]*p[i]<=x;i++)
    {
        if(x%p[i]==0)
        {
            int num=0;
            while(x%p[i]==0) x/=p[i],num++;
            if(num==1) res=res*p[i]*p[i];
            else res=res*p[i];
            if(res>=inf) return inf;
        }
    }
    if(x*x<=tx)res=res*x*x;
    else{// 这里大概就是判断炸没炸 1e10
        int s=sqt(x);
        if(s==-1)
        {
            res=res*x; if(res>=inf) return inf;
            res=res*x; if(res>=inf) return inf;
            return res;
        }
        res=res*s;
    }
    return res;
}
int n,a[300000];
signed main()
{
    pr[1]=1;
    for(int i=2;i<=2000;i++)
    {
        if(pr[i]==0) p[++tot]=i,pw[tot]=i*i*i;
        for(int j=1;j<=tot&&p[j]*i<=2000;j++)
        {
            pr[i*p[j]]=1;
            if(i%p[j]==0) break;
        }
    }
    read(n);
    for(int i=1;i<=n;i++) read(a[i]),a[i]=div(a[i]),cnt[a[i]]++;
    int ans=0;
    for(map<int,int>::iterator i=cnt.begin();i!=cnt.end();i++)
    {
        if((i->first)==1)
        {
            ans++;
            continue;
        }
        int rev=rebuild(i->first);
        if(rev==inf) {ans+=i->second;continue;}
        if(cnt.find(rev)==cnt.end()) {ans+=i->second;continue;}
        ans+=max(i->second,cnt[rev]);// 选多的那个
        cnt[rev]=0,cnt[i->first]=0;
    }
    printf("%lld",ans);
}
```

