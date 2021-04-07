---
title: 点分治
mathjax: true
index_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.2/2233/wallhaven-96w8e8-1.webp
banner_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.2/2233/wallhaven-96w8e8-1.webp
date: 2021-2-13 18:12:00
categories: 数据结构
tags: [点分树+点分治]
---









点分治 & 点分树 的一些理解。

<!--more-->

## 点分治

### 什么是点分治
点分治，是树分治的一种。

树分治包括了点分治，边分治，链分治。

点分治主要处理两点间路径信息的问题。

### 点分治怎么用

拿最基本的[问题](https://www.luogu.com.cn/problem/P3806)入手，给你一棵树，让你统计树上距离为 $k$ 的点对个数。

形式的说 $\sum\limits_{i=1}^{n}\sum\limits_{j=1}^{n}[dist(i,j)=k]$。

#### $naive\ sol1$

当我们知道一个端点 $i$ 的时候，我们可以 $\mathcal{O(n)}$ 的求出该点的贡献，每个点都 $\mathcal{O(n)}$ 计算，总复杂度$\mathcal{O(n^2)}$。

#### $naive\ sol2$

考虑分治，随机选一个点 $u$ ，把点 $u$ 删掉，算出剩下的每个连通块的子问题和。同时把经过点 $u$ 的链的答案统计出来。

由于分治，复杂度为 $\mathcal{O(dep\times n)}$ ,$dep$ 为分治层数。

#### $right\ sol$

发现让分治层数最小，本质上是让每次分治时产生的最大的连通块最小。因为此时分治层数可以看成最大的连通块变成 $1$ 的次数。

发现这个定义本质上就是找重心。在重心心处分治。

由于中心的性质，每一次分割后产生的最大的连通块 $maxsz\leq\lceil\frac{sz}{2}\rceil$

所以 $dep$ 一定是 $O(\log_{2}n)$  级别的。

至于合并，也就一个 $O(sz)$ 解决的问题。



---

## 点分树

### 什么是点分树

假如当前重心为 $u$ 我们把删去 $u$ 之后产生的新重心 $v_{1,2,\cdots,k}$ 向 $u$ 连边。

不难发现最终形态是一棵树。

我们把这棵树称作点分树。

### 点分树有什么用

观察点分治，我们每次合并答案都只在该连通块的重心处合并该连通块内的贡献。

所以假如谈一个点 $u$ 的贡献，那么他只可能在 $u$ 点，使 $u$ 点单独成为连通块所删除的重心 $v$，使 $v$ 点所在连通块被从一个大连通块剥离出来而删的重心 $z$，and  so on 。

 发现产生贡献的重心，全都在 点分树中 $u$ 到根的路径上。

所以我们统计答案相当于在统计一条链，而这个链的长度是 $\mathcal{O(\log_2n)}$ 级别的。

所以我们可以通过点分树来维护一些更难于直接记录的信息，以及一些修改操作。









对于点分治，点分树，有一点需要注意。

为什么点分治是对的？原因就在于，通过在重心时对链的合并，本质上就已经完成枚举树上的每一条路径了。

所以点分治本质上和那个 $\mathcal{O(n^2)}$ 的暴力时一样在枚举路径的。

所以在看到有树上问题问路径的一些情况，不妨考虑一下点分治。



## 练手推荐

[震波](https://www.luogu.com.cn/problem/P6329)

[捉迷藏](https://www.luogu.com.cn/problem/P2056)

[Qtree4](https://www.luogu.com.cn/problem/P4115)

[开店](https://www.luogu.com.cn/problem/P3241)

[树上游戏](https://www.luogu.com.cn/problem/P2664)

本人实力有限，都是模板题了/kk





