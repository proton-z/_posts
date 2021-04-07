---
title: wc2021 t3 fib
mathjax: true
index_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.2/2233/cd02a5048846677686a7b0c302135a66.jpg
banner_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.2/2233/cd02a5048846677686a7b0c302135a66.jpg
date: 2021-2-14 19:13:00
categories: 数学
tags: [数论,fib循环节,人类智慧题]
---

wc 2021 的 T3。

<!--more-->



今天有点晚了，写一下这个题的大体思路。

首先由题意：
$$
F_n=F_{n-1}+F_{n-2},F_0=a,F_{1}=b
$$
记录斐波那契数列为 $f_i,f_0=0$ ，特殊定义 $f_{-1}=1$。
$$
有\ F_n=a\cdot f_{n-1}+b\cdot f_{n}
$$
问题转换成 给你 $a,b$,让你求使得 $a\cdot f_{n-1}+b\cdot f_{n}\equiv 0\pmod{m}$ 最小的 $n$。

1.  首先，当 $m$ 为质数的时候很好做，因为 $a,b,f_n,f_{n-1}$ 的逆元都存在，可以直接移项，做除法。

2.  其次由于斐波那契在 $\bmod m$ 的情况下是纯循环的（暂时不会证明），假如 $m=\prod p_i$ ，我们也是也已轻松合并的。

3.  但是并不是所有的 $m$ 都可以分解成一堆会不相同的素数积，考虑 $m=\prod p_i^{\alpha_i}$，合并似乎还是很好合并。（本质上就是求一堆同余方程组的最小解），问题转化为解决 $m=p^{\alpha}$ 的问题。

4.  $update$ 我想了一下，发现合并并不显然，这里可能是最重要的一步，

   假设现在我们有一个对于 $F_x \bmod p^\alpha=0$ 的最小解，而他的循环节可能并不是斐波那契 $f_x \bmod p^\alpha=0$ 的循环节。

   有结论 $F_x \bmod p^\alpha=0$ 的循环节，肯定是 $f_x \bmod p^\alpha=0$ 的循环节的因数。

   证明：

   1. 显然 $f_x\bmod p^\alpha =0$ 的循环节肯定是 $F_x\bmod p^\alpha=0$ 的循环节，根据 $F_n$ 表达式可知。

   2. 设 $F_x\bmod p^\alpha=0$ 最小解为 $x_1$，第二小解为 $x_2$ 。

      那么有结论 $x2-x1$ 是一个循环节，证明可能比较感性。

      当前 $F_x$ 数列长得样子应该是：
      $$
      \cdots,-k,0,k,k,2k,\cdots,f_i\times k,\cdots,-t,0,t,2t,\cdots
      $$
      就是 $0\equiv f_i\times k\pmod{p^\alpha}$,有两种可能 $p^\alpha\mid f_i$,  $p^\beta\mid k,p^{\alpha-\beta}\mid f_i$ 。

      第一种可能证明显然，第二种可能因为有 $t$ 是数倍的 $k$ ，那么显然也有 $p^\beta\mid t$ ,接下来的证明显然。

   3.  如果这个新循环节不是 $f_x\bmod p^\alpha=0$ 的循环节，那可以根据类似上面的证明，证明出 $(l_1,l_2)$  也是一个循环节，$l_1,l_2$ 分别为之前的两个循环节（新循环节，和斐波那契自带的循环节）。 $(l_1,l_2)$ 显然也是 $l_2$ 的一个因数。

   如是我们为了找到解的循环节可以去找斐波那契循环节的因数，然后判断该长度是否为循环节。这样一定可以找到最小的循环节。 

   $10^5$ 范围内数的因子 $\leq 128$ 此处暴力即可。

5.  由于 $m=p^{\alpha}$ ,逆元可能不存在，按照基本套路，我们使用乘法方程，并且提取每一个数的 $p$ 因子

   为了之后表达方便，令 $a',b'$ 表示现在的 $a,b$。
   $$
   a'=a\cdot p^{A}\ ,\ b'=b\cdot p^{B}\ ,\ f_{n-1}=c\cdot p^{C}\ ,\ f_{n}=d\cdot p^D\ ( \ a,b,c,d\perp p)
   $$
   原方程化为：
   $$
   a\times p^A\cdot c\times p^C+b\times p^B\cdot d\times p^D\equiv 0\pmod{p^\alpha}
   $$

   $$
   (ac)\times p^{A+C}+(bd)\times p^{B+D}\equiv 0\pmod{p^\alpha}
   $$

   这个方程成立有两种情况：

   1. $A+C\ge \alpha$,$B+D\ge \alpha$。
   2. $ac\equiv bd\pmod{p^{\alpha-(A+C)}}$，且 $A+C=B+D$

   成立条件：

   1. 在线处理 $C\ge \alpha-A,D\ge \alpha-B$ 相当于二维数点（可能会有简单方法？）（由于 $A,B,C,D$ 都很小直接暴力就行）。

   2. 显然等价:
      $$
      \frac{a}{b}\equiv \frac{d}{c}\pmod{p^{\alpha-(A+C)}},A-B=D-C
      $$

6.  预处理 ，对每一个 $m$ 的质因数 $p_i$，首先处理出成立条件 $1$ 成立的答案。其次考虑成立条件 $2$ ,预处理的过程此时我们知道 $C,c,D,d,\alpha$ ，不知道 $A,a,B,b$ 。发现 $A\leq \log_{p}^{m}$ , 即$A$ 的值域很小 ，此时暴力枚举 $A$ ，此时可以计算出 $B$ 的取值，从而可以处理出 $\frac{d}{c}\bmod p^{\alpha-(A+C)}$,将其存进表。 

   不难发现，此处复杂度为枚举 $A$ 的复杂度，即 $\mathcal{O(\sum\limits _{i=1}^{k}p_i^{\alpha_i}\alpha_i)}$或者$\mathcal{O(\sum\limits _{i=1}^{k}p_i^{\alpha_i}\alpha_i^2)}$ 。(看你成立条件 $1$ 的具体处理方法)。

   询问时候，对于每个 $m$ 的质因数 $p_i$ ，我们可以枚举 $C$ 然后，可以算出 $\frac{a}{b} \bmod {p^{\alpha-(A+C)}}$ 的值，暴力查表，找到一组特解。

   不难发现，此处的复杂度为枚举 $C$ 的复杂度，和寻找循环节的复杂度，即 $\mathcal{O(\sum\limits_{i=1}^{k}d(3p_i^{\alpha_i})+\sum \limits _{i=1}^k\alpha_i)}$。

   



代码写的可能比较丑。



``` cpp
#include<bits/stdc++.h>
#include<tr1/unordered_map>
using namespace std;
#define int long long
int T,m;
inline void read(int &x)
{
    char c=getchar();x=0;
    while(c>'9'||c<'0') c=getchar();
    while(c<='9'&&c>='0') x=(x<<1)+(x<<3)+c-'0',c=getchar();
}
vector<int> divs[300010];
int p[10],k[10],t[10],loop[10];
int f[10][300010];
inline int gcd(int a,int b){if(b==0) return a;return gcd(b,a%b);}
inline int qpow(int a,int b,int p)
{
    int k=1;
    while(b){if(b&1) k=k*a%p;a=a*a%p,b=b>>1;}return k;
}
inline int ppow(int a,int b)
{
    int k=1;
    while(b){if(b&1) k=k*a;a=a*a,b=b>>1;}return k;
}
namespace Inv
{
    int phi[100010],pr[100010],p[100010],tot=0;
    inline void init()
    {
        for(int i=2;i<=100000;i++)
        {
            if(pr[i]==0) p[++tot]=i,phi[i]=i-1;
            for(int j=1;j<=tot&&p[j]*i<=100000;j++)
            {
                pr[i*p[j]]=1;
                if(i%p[j]==0)
                {
                    phi[i*p[j]]=p[j]*phi[i];break;
                }
                phi[i*p[j]]=(p[j]-1)*phi[i];
            }
        }
    }
    inline int inv(int x,int m){return qpow(x,phi[m]-1,m);}
}

inline pair<int,int> nump(int x,int p,int ki)
{
    int k=0;
    if(x==0) return make_pair(ki,0);
    while(x%p==0) k++,x/=p;
    return make_pair(min(k,ki),x);
}
inline void exgcd(int a,int b,int &x,int &y)
{
    if(b==0){x=1,y=0;return ;}
    exgcd(b,a%b,y,x); y-=(a/b)*x;
}
int can[10][20][20];
tr1::unordered_map<int,int> z[10][20][20][40];
signed main()
{
    Inv::init();
    read(T),read(m);
    int mm=m;
    int tot=0;
    for(int i=2;i*i<=m;i++)
    {
        if(m%i==0)
        {
            p[++tot]=i;t[tot]=1;k[tot]=0;
            while(m%i==0) m/=i,t[tot]*=i,k[tot]++;
        }
    }
    int MAXX=0;
    if(m>1) p[++tot]=m,t[tot]=m,k[tot]=1;
    for(int i=1;i<=tot;i++)
    {
        f[i][0]=0;f[i][1]=1;
        for(int j=2;j<=6*t[i];j++)
        {
            
            f[i][j]=(f[i][j-1]+f[i][j-2])%t[i]; 
            if(f[i][j-1]==0&&f[i][j]==1)
            {
                loop[i]=j-1; // 0-> loop -> 2loop ...and so on 
                MAXX=max(MAXX,loop[i]);
                break;
            }
            pair<int,int> vc=nump(f[i][j-1],p[i],k[i]),vd=nump(f[i][j],p[i],k[i]);
            int C=vc.first,c=vc.second,D=vd.first,d=vd.second;
            for(int a=max(0ll,k[i]-C);a<=k[i];a++) 
                for(int b=max(0ll,k[i]-D);b<=k[i];b++) can[i][a][b]=j;
            int tmp=ppow(p[i],k[i]-C);
            for(int A=0;A+C<k[i];A++)
            {
                int res=d*Inv::inv(c,tmp)%tmp;
                if(z[i][A][D-C+k[i]][C].count(res)==0)
                z[i][A][D-C+k[i]][C][res]=j;
                tmp/=p[i];
            }// z : p_i , A -> D-C  -> d/c
        } 
        for(int j=2;j<=loop[i]*2;j++) f[i][j]=(f[i][j-1]+f[i][j-2])%t[i];
    }
    /////////////////
    for(int i=1;i<=MAXX;i++)
    {
        for(int j=1;j*i<=MAXX;j++) divs[i*j].push_back(i);
    }
    m=mm;
    int a0=0,m0=1;
    while(T--)
    {
        int a,b,aa,bb,A,B;
        read(aa),read(bb);
        aa%=m,bb%=m;
        if(aa==0) {cout<<0<<endl;;continue;}
        if(bb==0) {cout<<1<<endl;continue;}
        a0=0,m0=1;bool FLG=0,fag=0;
        for(int i=1;i<=tot;i++)
        {
            pair<int,int> va=nump(aa,p[i],k[i]),vb=nump(bb,p[i],k[i]);
            a=va.second,A=va.first;
            b=vb.second,B=vb.first;
            int ans=-1,tmp;
            if(aa%t[i]==0) {ans=0;goto chk;}
            if(bb%t[i]==0) {ans=1;goto chk;}
            if(can[i][A][B]!=0) {ans=can[i][A][B];goto chk;}
            if((a*f[i][0]+b*f[i][1])%t[i]==0){ans=1;goto chk;}
            tmp=ppow(p[i],k[i]-A);
            for(int C=0;A+C<k[i];C++)
            {
                int res=a*Inv::inv(b,tmp)%tmp;
                res=-res+tmp+tmp;
                res%=tmp;
                if(z[i][A][A-B+k[i]][C].count(res))
                {
                    ans=z[i][A][A-B+k[i]][C][res];
                    goto chk;
                }
                tmp/=p[i];
            }
            chk:;
            ans%=loop[i];
            if(ans==-1) {
                FLG=1;
                cout<<-1<<endl;break;
            }
            int M=0,A=ans;
            for(auto x:divs[loop[i]])
            {
                if((aa*f[i][x+ans-1]+bb*f[i][x+ans])%t[i]==0) 
                {
                    M=x;break;
                }
            }
            /////////////////////////////////////
            int g=gcd(M,m0);
            if(abs(A-a0)%g!=0){
                fag=1;cout<<-1<<endl;break;
            }
            int x,y;
            exgcd(m0/g,M/g,x,y);
            x%=(M/g),x+=(M/g),x%=(M/g);
            int MM=m0;
            m0=m0/g*M;x=x*((A-a0)/g)%m0+m0;x%=m0;
            a0=x%m0*MM%m0+a0;a0%=m0;
            a0+=m0;a0%=m0;
        }
        if(FLG==1||fag==1) continue;
        printf("%lld\n",a0);
    }
}
```



   