---
title: arc122D
mathjax: true
index_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.9/2233/a9.png
banner_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.9/2233/a9.png
tags: [trie]
date: 2021-6-13 19:43:00
categories: 数据结构

---

### 题意

[Link](https://atcoder.jp/contests/arc122/tasks/arc122_d)

### 题解

#### hint1

考虑权值 $v$ 的合法性。

如果 `Alice ` 任意选一个 $a_i$ ,`Bob` 都有一个对策 $a_j$, $s.t. \ a_i\operatorname{xor} a_j\leq v$，那么 $v$ 就是合法的。

#### hint2

想要让 $v$ 尽可能大，所以从二进制的高位到低位依次考虑。

#### solution

建出全局的 trie.

1. 对于第 $x$ 位（当前最高位） ，如果是该位是 $0$ 的数的个数与是 $1$ 的数的个数都是偶数，

   那么 `Bob` 完全可以当 `Alice` 选一个这位是 `0` 的数时，也选另一个该位为 `0`的数；选该位为`1`的数时候也选另一个该位为`1`的数，从而使这位 $\operatorname{xor}$ 都为`0`。

   问题变成了两个子问题，相当于在 `trie` 从节点 $u$ 的问题 ，化归到 $x$ 的两个儿子的独立问题。

2. 否则，我们必须选择一个该位为 $0$,一个该位为 $1$ 的数。然后问题变为上述情况。

   注意如果变成上述情况，该位 $\operatorname{xor}$ 只能为 $0$，而如果选择一个该位为 $0$,一个该位为 $1$ 的数，那么此位 $\operatorname{xor}$ 就是 $1$，由于是最高位，显然 $1$ 更优。

   这时，问题转化为要在两个集合 $S_1,S_2$ 中选 $x\in S_1$,使得 $\min_{y\in S_2}{x\operatorname{xor}y}$ 最大。

   我们可以一个一个插入trie中，在另一个集合一个一个查询 $\min$ 然后再取$\max$。

注意我们分治是保证了 $\sum size=n$ 一定。

然后情况2，的复杂度是 $\mathcal{O(size\times T)}$。

所以均摊下来是 $\mathcal{O(nT)}$

