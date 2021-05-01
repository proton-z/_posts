---
title: 基本数论
mathjax: true
index_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.4/2233/83110343_p9.jpg
banner_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.4/2233/83110343_p9.jpg
tags: [数论,miller-rabin,pollard-rho,分解质因数]
categories: 数学
---







说一下一些基本数论算法的实现和简洁证明。



持续更新中。

<!--more-->

1. $exgcd$

   目的：求解方程 $ax+by=(a,b)$ 的一组特解。

   做法：~~由于他叫exgcd~~，我们就按求 $\gcd$ 方法试一下。
   $$
   \begin{aligned}
   不妨设 (a,b)=1,a>b\\
   a\cdot x+b\cdot y=&1\\
   b\cdot y+(a\bmod b+\lfloor\frac{a}{b}\rfloor b)\cdot x=&1\\
   提取b\\
   b\cdot(y+\lfloor \frac{a}{b}\rfloor x)+(a\bmod b)x=&1\\
   问题化归，求解\ bx_1+(a\bmod b)y_1=&1\\
   \end{aligned}
   $$
   $\mathcal{Code}$

   ``` cpp
   inline void exgcd(int a,int b,int &x,int &y)
   {
       if(b==0){x=1,y=0;return ;}
       exgcd(b,a%b,y,x); y-=(a/b)*x;
   }
   ```

   

   为啥能求逆元？
   $$
   \begin{aligned}
   ax+by&=1\\
   ax+by&=1\pmod{b}\\
   ax&=1\pmod{b}
   \end{aligned}
   $$
   显然 $x$ 是 $a$  在 $\bmod b$ 意义下的逆元

2. $excrt$ 

   目的：合并若干线性同余方程组。
   $$
   \begin{cases}
   x\equiv a_1\pmod{p_1}\\
   x\equiv a_2\pmod{p_2}\\
   \ \ \ \ \vdots\\
   x\equiv a_n\pmod{p_n}
   \end{cases}
   $$
   问题本质等价，合并两个方程。

   $x\equiv a_1\pmod{p_1},x\equiv a_2\pmod{p_2}$

   若存在解则解一定能表达成 $x=x_0+k\cdot lcm(p_1,p_2)$,$x_0$ 是一个特解。

   显然 $p_1\mid lcm(p_1,p_2),p_2\mid lcm(p_1,p_2)$。

   现在目的找到这样的一个特解 $k$。
   $$
   k=k_1\cdot p_1+a_1,k=k_2\cdot p_2+a_2\Rightarrow k_1\cdot p_1+a_1=k_2\cdot p_2+b_2\\
   \Rightarrow k_1\cdot p_1-k_2\cdot p_2=b_2-b_1
   $$
   可以用 $exgcd$ 求一组 $k_1,k_2$ 特解。

   ·

   

3. $Miller\ rabin$

   1. 首先费马小定理判断一波,$p\in \mathtt{prime} \Leftrightarrow \forall (a,p)=1,a^{p-1}\equiv1\pmod{p}$。

   2. 然后二次探测。

      就是如果 $a^{2}\equiv 1\pmod{p}$,如果 $p$ 是质数，那么必定有 $p\mid(a-1)$,或者 $p\mid(a+1)$。

      否则 $p$ 是合数。

      $a^{p-1}\equiv 1\pmod{p}$。

      此时如果 $p$是质数，$a^{\frac{p-1}{2}}\equiv 1\ or\ -1\pmod{p}$，如果 $\equiv 1$ 我们就继续探测。

      本质上我们就是通过看 $a^b\equiv 1\pmod{p}$ 这种形式来看 $p$ 是否为质数。

      ``` cpp
      inline bool mr(int x,int p)
      {
          int b=x-1;
          while(!(b&1)) b>>=1;
          int k=qpow(p,b,x);
          while(b<=x-1)
          {
              int muls=k*k%x;
              if(muls==1&&(k!=1&&k!=x-1)) return 0;
              k=muls;b<<=1;
          }
          if(k!=1) return 0;
          return 1;
      }
      int p[]={2,3,5,7,11,13,15,17,19,23};
      inline bool judge(int x)
      {
          if(!(x&1)) return 0;
          for(int i=0;i<=8;i++)
          {
              if(x==p[i]) return 1;
              if(mr(x,p[i])==0) return 0;
          }
          return 1;
      }
      ```

      当 $p$ 的值域是`long long` 范围时，乘法可能会需要快速乘。

4. 找阶&原根。

   这个东西说实话只要真正了解定义就能如同行云流水般解决了。

   1. 阶

      最小的使得 $a^x\equiv 1\pmod{p}$ 的正整数 $x$,被称为 $a\bmod p$ 的阶。

      由于 $a^{\phi(p)}\equiv 1\pmod{p}$,所以 $ord_p(a)\mid\phi(p)$。

      证明很显然，如果不整除，可以用类似辗转相除的方法推出，$\exists \ x<ord_p(a),a^x\equiv 1\pmod{p}$。

      求法可以把 $\phi(p)$ 的质因子一个一个消去，来看是否有 $a^{\frac{\phi(p)}{k}}\equiv 1\pmod{p}$。

      复杂度 $O(pollard-\rho)+\log(n)$。

   2. 原根

      满足 $ord_p(a)=\phi(p)$ 的 $a$ 被叫做 $p$ 的一个原根。

      原根性质：若 $g$ 为 $p$ 其中一个原根，那么 $1,g,g^2\cdots g^{\phi(p)-1}$ 互不相同。

      证明可以用反证法。

      由于 如果 $(k,p)=1$,有 $0,k,2k,\cdots,(p-1)k\bmod p$ 一定互不相同。

      也是反证，如果 $xk\equiv yk\pmod{p}$,那么 $(x-y)k\equiv 0\pmod{p}$,与 $(k,p)=1$ 矛盾。

      所有 $p$ 有 $\phi(\phi(p))$ 个原根。

      原根有很多，随机找可以在 $O(\frac{p}{\phi(\phi(p))})$ 的期望次找到。

      设 $\phi(p)=\prod p_i^{\alpha_i}$。

      那么判断一个数 $x$ 是不是原根就看 是否 $\forall \ k,x^\frac{\phi(p)}{p_k}\not \equiv 1\pmod{p}$。

      复杂度 $O(pollard-\rho)+\frac{p}{\phi(\phi(p))}\log(n)$。

      

