---
title: 基本多项式
mathjax: true
index_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.4/2233/DmuXj0eU8AAPOMZ.jfif
banner_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.4/2233/DmuXj0eU8AAPOMZ.jfif
tags: [多项式,gf]
categories: 数学

---

本文简单介绍一下基本多项式理论。

持续更新中。

<!--more-->

因为是简单介绍，只说明做法，不给予严格证明，以及前人是如何想出的这个方法。 

### 多项式乘法

#### 多项式乘法与和卷积

$A=\sum\limits_{i=0}^{n}a_ix^i,B=\sum\limits_{i=0}^{n}b_ix^i$。

显然有 $A\times B=\sum\limits_{i=0}^n\sum\limits_{j=0}^na_ib_jx^{i+j}=\sum\limits_{d=0}^n\sum\limits_{i+j=d}^na_ib_jx^d$。

$C=A\times B,C(d)=\sum\limits_{i+j=d}^na_ib_jx^d$。

这玩意就是和卷积，可见和卷积与多项式乘法有着千丝万缕的关系。

#### 快速傅里叶变换

众所周知，一个 $n-1$ 次多项式（函数）可以用 $n$ 个不同的点唯一确定出来。

而如果考虑两个多项式 $f,g$，当$x=x_0$ 时，$f\times g\ (x_0)=f(x_0)\times g(x_0)$ 。

这个很好理解。

所以点值表达乘积就是直接将相同横坐标的 $y$ 乘。

那么如何将多项式 $f$ 转成点值。

代入复数，用 $n$ 次单位根做 $x$。

即 $x^n=1$ 的 $n$ 个复数解，记录以 $x$ 轴正方向，逆时针第一个复根为 $w$，所有单位根就是 $w^0,w^1,\cdots,w^{n-1}$。

目标：求 $\forall \ i,F(k)=\sum\limits_{j=0}^{n-1}a_j(w^i)^j$。

我们将次方奇数偶数分开考虑 $F(k)=\sum\limits_{j=0}^{n}a_j(w^k)^j=\sum\limits_{j=0}^{n/2-1}a_{2j}(w^k)^{2j}+w^k\sum\limits_{j=0}^{n/2-1}a_{2j+1}(w^k)^{2j}$。

$F(k)=\sum\limits_{j=0}^{n}a_j(w^k)^j=\sum\limits_{j=0}^{n/2-1}a_{2j}(w^{2k})^{j}+w^k\sum\limits_{j=0}^{n/2-1}a_{2j+1}(w^{2k})^{j}$。

由于 $(w^k)^2=(w^{k+n/2})^2$ （$w^n=1$)。

所以 $F(k)$ 与 $F(k+n/2)$ 只差一点点，就是 $w^k$ 项的正负号 （$w^k=-w^{k+n/2}$）。

原本你要求 $F(w^0),F(w^1),\cdots F(w^{n-1})$,现在只要求 $F(w^0),F(w^1),\cdots F(w^{n/2-1})$。

范围直接缩小一倍。那么对于接下来的 $F_0(w^{2k}),F_1(w^{2k})$（系数是奇数(1)还是偶数(0)）也可以递归下求。

递归效率缓慢，能够优化。

发现本质上每次将 $k$,缩小一半的操作其实是想要最后把 $w^0$ 算出，然后计算出别的。

可以如图按照一定顺序把 $a_i\times (w^0)^i$ 按顺序排开，然后合并。

 ![](\img\fft1.png)

大概。。。

这个顺序**通过观察** 发现是二进制反过来 ，所以能快速求出。

代码解读一下。

``` cpp
const pie=acos(-1);
inline void fft(complex *a)
{
	for(int i=0;i<lim;i++) if(i<rev[i]) swap(a[i],a[rev[i]]);
	for(int mid=1;mid<lim;mid=mid<<1)//从下向上合并，mid 代表合并之前每块的长度
	{
		complex wn(cos(p/mid),sin(p/mid));// 合并后那个的w^k,模长都是1，用角度表示。
                                          // 比如说 mid=lim/2 时,wn这个的角度就是 pie/(lim/2)=(2*pie)/lim
        								  // 就是最初的单位根。
		for(int r=mid<<1,j=0;j<lim;j+=r)//跳块，r就是合并后块长
		{
			complex w(1.0,0.0);
			for(int k=0;k<mid;k++,w=w*wn) //w 就是你乘的 w^k (因为你更新得更新 w^0,w^k,w^2k,... 处的点值)
			{
				complex x=a[k+j],y=w*a[k+j+mid];
				a[k+j]=x+y;
				a[k+j+mid]=x-y;
			}
		}
	}
}
```



一般人不用点值表达，所以必须要有逆傅里叶变换。

**不难发现** 由于范德蒙德矩阵在 $A_{i,j}=w^{ij}$ 有着巨NB的性质，$A^{-1}_{i,j}=w^{-ij}$。 （前面那个 $-1$ 的意思是逆矩阵）

所以只要把上文代码中的 `wn(cos(p/mid),sin(p/mid))` 改成 `wn(cos(-p/mid),sin(-p/mid))` 即可。

证明是个构造证明，我也不会从暴力手算 $A_{i,j}$ 逆矩阵出发证明，而只会验证 $A\times B=\epsilon$,$B$ 即为你构造的那个逆矩阵。



#### 快速数论变换

把单位根换成原根有相同性质。

