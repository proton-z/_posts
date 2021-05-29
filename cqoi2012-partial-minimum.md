---
title: 局部最小值
mathjax: true
index_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.9/2233/a8.jpg
banner_img: https://cdn.jsdelivr.net/gh/protons-z/cdn@1.9/2233/a8.jpg
tags: [dp,idea题]
categories: dp

---

[Link](https://www.luogu.com.cn/problem/P3160)

有意思的 $dp$。

---

首先不难想到把 $X$ 与周围建出偏序关系的 $dag$，转化出来的问题大概是问你这个 $dag$ 有多少种拓扑序。

但是你会发现，你给定 $dag$ 的拓扑序有可能 “夹带私货”。

就是体现不出来 . 与周围的关系，可能存在某个 . 在你给定的拓扑序中其实是 $X$。

这个很好办，容斥即可。

---

那么你怎么算给定的 $dag$ 的拓扑序呢？

发现这个 $dag$ 只有两层，一层入度为 0，而这层入度为 0的就是那些 $X$ ，考虑合法情况 $X$ 最多有 8个。

不妨设 $num$ 为 $X$ 的个数，现在问题似乎变成了：把剩下的 $nm-num$ 个点（出度为0）**合法地**插入这 $num$ 数”前后左右“。

设剩下的点其中一个为 $x$。

$x$ 要是想插入，那么所有 $(u,v)$ 的 $u$ 必须已经被插入。

$num\leq 8$,$num$ 很小，我们就状压 $num$ 。 

设 $f_{s,x}$ 表示,当前那 $num$ 个数的状态为 $s$，剩下有 $x$ 个自由的，出度为0的，可以插入的点。

注意以下 $dp$ 方程默认从 $f_{s,x}$ 转移出去。

1. $f_{s,u}\leftarrow f_{s,u}+\binom{x}{u}(x-u)! f_{s,x} (u\leq x)$
2. $f_{s|point(j),x+add}\leftarrow f_{s|point(j),x+add}+f_{s,x}\ (point(j)\not \subset s)$

第一个表示，从剩下 $x$ 个自由的点中选 $x-u$ 个，然后把这 $x-u$ 个全排列插入序列尾。

第二个表示，选一个没选过的 $point(j)$ ,然后能新选 $add$ 个自由点，这个 $add$ 可以转移时计算。

---

$dp$ 想怎么写就可以怎么写了，根据你状压的写法。

剩下的就只有一个 $dfs$ 枚举选的 $X$ 的点，然后容斥算一下即可。

``` c++
#include<bits/stdc++.h>
using namespace std;
int n,m,vd;
char c[10][10];
bool vs[10][10];
vector<pair<int,int> >r;
int v[6][9];
int dx[]={1,1,1,-1,-1,-1,0,0,0};
int dy[]={-1,1,0,-1,1,0,-1,1,0};

inline void paint(int x,int y){
    for(int i=0;i<=8;i++){
        if(v[dx[i]+x][dy[i]+y]==0&&c[dx[i]+x][dy[i]+y]) vd--;
        v[dx[i]+x][dy[i]+y]++;
    }
}
inline void cls(int x,int y){
    for(int i=0;i<=8;i++){
        v[dx[i]+x][dy[i]+y]--;
        if(v[dx[i]+x][dy[i]+y]==0&&c[dx[i]+x][dy[i]+y]) vd++;
    }
}
int num=0;
////////////////////////////////////// dp begin
vector<int> b[20];
inline int getnum(int x){
    int res=0;while(x){
        x-=(x)&(-x);
        res++;
    }return res;
}
int dp[260][30];int p[6][9];
const int mod=12345678;int f[100];
int comb[30][30];
inline int solve(){
    memset(dp,0,sizeof(dp));memset(p,0,sizeof(p));
    for(int i=0;i<r.size();i++){
        pair<int,int> x=r[i];
        for(int k=0;k<8;k++){
            p[x.first+dx[k]][x.second+dy[k]]|=(1<<i);
        }
    }
    int av=0;for(int i=1;i<=n;i++) for(int j=1;j<=m;j++) if(v[i][j]==0) av++;
    dp[0][av]=1;
    for(int i=0;i<=r.size();i++) b[i].clear();
    for(int i=0;i<=(1<<r.size())-1;i++){
        b[getnum(i)].push_back(i);
    }
    for(int i=0;i<=r.size();i++){
        for(auto s:b[i]){//status
            for(int j=0;j<=n*m-r.size();j++){
                for(int k=0;k<j;k++){
                    dp[s][k]+=1ll*dp[s][j]*f[j-k]%mod*comb[j][k]%mod;
                    dp[s][k]%=mod;
                }
            }
            for(int j=0;j<r.size();j++){//choose s | 1<<j
                if(((1<<j)&s)==0){
                    int ts=(1<<j)|s;
                    int res=0;
                    for(int k=0;k<8;k++){
                        int tx=r[j].first+dx[k],ty=r[j].second+dy[k];
                        if((p[tx][ty]|ts)==ts&&c[tx][ty]) res++;
                    }
                    for(int k=0;k<=n*m-r.size();k++){
                        dp[ts][k+res]+=dp[s][k];dp[ts][k+res]%=mod;
                    }
                }
            }
        }
    }
    return dp[(1<<(r.size()))-1][0];
}
//////////////////////////////////////////dp end
int ans=0;
inline void dfs(int X,int nx,int ny){
    int res=solve();
    if((r.size()-num)&1) ans-=res;
    else ans+=res;ans%=mod;
    if(vd==0)
        return ;
    for(int i=nx;i<=nx;i++)for(int j=ny;j<=m;j++){
        if(v[i][j]) continue;
        paint(i,j);
        r.push_back(make_pair(i,j));
        dfs(X+1,i,j);
        cls(i,j),r.pop_back();
    }
    for(int i=nx+1;i<=n;i++)for(int j=1;j<=m;j++){
        if(v[i][j]) continue;
        paint(i,j);
        r.push_back(make_pair(i,j));
        dfs(X+1,i,j);cls(i,j);r.pop_back();
    }
}
signed main(){
    cin>>n>>m;
    f[0]=1;
    for(int i=1;i<=n*m;i++) f[i]=1ll*f[i-1]*i%mod;
    for(int i=0;i<=n*m;i++) comb[i][0]=1;
    for(int i=1;i<=n*m;i++){
        for(int j=1;j<=i;j++){
            comb[i][j]=(comb[i-1][j]+comb[i-1][j-1]);
            comb[i][j]%=mod;
        }
    }
    for(int i=1;i<=n;i++) cin>>(c[i]+1);
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++){
            if(c[i][j]=='X'){
                paint(i,j);r.push_back(make_pair(i,j)),num++;
            }
        }
    }vd=0;
    for(int i=1;i<=n;i++){
        for(int j=1;j<=m;j++) if(v[i][j]==0) vd++;
    }
    dfs(1,1,1);
    cout<<(ans+mod)%mod;
}

```



可能是我写的比较渣，感觉还是大概能看明白的吧。