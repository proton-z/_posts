---
title: noi2016循环之美
mathjax: true
index_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.4/84655195_p0.jpg
banner_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.4/84655195_p0.jpg
date: 2021-3-28 23:12:00
categories: 数学
tags:  [筛法,整除分块,idea题]
---

[循环之美](https://www.luogu.com.cn/problem/P1587)

### step1

首先根据小学知识，如果 $k=10$ ,那么如果分数 $\frac{a}{b},(a,b)=1$ 想要纯循环，必须满足 $2\not\mid b,5\not\mid b$。

可以猜测如果 $k\not =10$ 时，$\frac{a}{b}$ 纯循环 $\Leftrightarrow$ $(b,k)=1$。

那么如何证明呢?

只要余数出现循环代表商出现循环。

考虑余数形式一定是 $a,ak\bmod b,ak^2\bmod b,ak^3\bmod b,\cdots$。

如果有 $ak^i\equiv ak^j\pmod{b}$,由于 $(a,b)=1$,所以 $a$ 存在逆元，所以 $k^i\equiv k^j\pmod{b}$。

当 $(k,b)=1$,$k$ 存在逆元，显然有 $1\equiv k^{j-i}\pmod{b}$，是纯循环。

$\frac{k^{i}}{g^t}\equiv\frac{k^j}{g^t}\pmod{\frac{b}{g^t}}$,由于一次可能除不尽，所以 $i$ 为最小的使 $(\frac{k^i}{g^t},\frac{b}{g^t})=1$ 的 $i$, $g=\gcd(k,b)$。

那么 原始化为  $\frac{k^{i}}{g^t}\equiv\frac{k^i}{g^t}k^{j-i}\pmod{\frac{b}{g^t}}$,$k^{j-i}\equiv 1\pmod{\frac{b}{g^t}}$，这个的循环节显然会比 $k^{j-i}\equiv1\pmod{b}$ 要小，所以不是纯循环。



### step2

问题分析的很清楚了。

有拆第一个 $gcd=1$ 的；有拆第二个 $gcd=1$ 的，而我选择都拆。。。
$$
\begin{aligned}
&\sum_{i=1}^m\sum_{j=1}^n[(i,k)=1][(i,j)=1]\\
&\sum_{i=1}^m[(i,k)]=1\sum_{j=1}^n\sum_{d\mid i,d\mid j}\mu(d)\\
&\sum_{d=1}^n\mu(d)\sum_{d|i}^m[(i,k)]=1\sum_{j=1,d|j}^n1\\
&\sum_{d=1}^n\mu(d)\sum_{d|i}^m\ \ \sum_{t|i,t|k}\mu(t)\lfloor\frac{n}{d}\rfloor\\
&\sum_{t|k}\mu(t)\sum_{d=1}^n\lfloor\frac{n}{d}\rfloor\mu(d)\sum_{d|i,t|i}^m1\\
&\sum_{t|k}\mu(t)\sum_{d=1}^n\lfloor\frac{n}{d}\rfloor\mu(d)\frac{m}{lcm(d,t)},\frac{m}{lcm(d,t)}=\frac{m(d,t)}{d\cdot t}\\
&\sum_{t|k}\mu(t)\sum_{d=1}^n\lfloor\frac{n}{d}\rfloor\mu(d)\lfloor\frac{m(d,t)}{dt}\rfloor\\
&\sum_{t|k}\mu(t)\sum_{g|t}\sum_{d=1}^n\lfloor\frac{n}{d}\rfloor\mu(d)\lfloor\frac{mg}{dt}\rfloor[(d,t)=g]\\
&\sum_{t|k}\mu(t)\sum_{g|t}\sum_{d=1}^{n/g}\lfloor\frac{n}{dg}\rfloor\mu(dg)\lfloor\frac{m}{dt}\rfloor[(d,t)=1]\\
&\sum_{t|k}\mu(t)\sum_{g|t}\sum_{h|t}\mu(h)\sum_{d=1}^{n/gh}\lfloor\frac{n}{dgh}\rfloor\mu(dgh)\lfloor\frac{m}{dth}\rfloor\\
\end{aligned}
$$
冷静一下。

---

$$
\begin{aligned}

&\sum_{t|k}\mu(t)\sum_{g|t}\sum_{h|t}\mu(h)\sum_{d=1}^{n/gh}\mu(dgh)\lfloor\frac{\frac{n}{gh}}{d}\rfloor\lfloor\frac{\frac{m}{th}}{d}\rfloor\\
\end{aligned}
$$

整除分块的形式一目了然。

问题转化为求解 $\sum_{i=1}^{n}\mu(Ti)$。
$$
\begin{aligned}
g(n,T)=&\sum_{i=1}^{n}\mu(Ti)\\
=&\sum_{i=1}^{n}\mu(T)\mu(i)[(T,i)=1]\\
=&\mu(T)\sum_{i=1}^{n}\mu(i)\sum_{f|i,f|T}\mu(f)\\
=&\mu(T)\sum_{f|T}\mu(f)\sum_{i=1}^{n/f}\mu(if)=\mu(T)\sum_{f|T}\mu(f)g(n/f,f)\\
\end{aligned}
$$
这个直接记忆化。

然后做完了。