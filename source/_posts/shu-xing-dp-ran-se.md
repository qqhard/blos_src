title: hdu-5290 Bombing plan 树形dp染色问题
date: 2015-10-06 00:27:59
tags:
- hdu
- 树形dp
- dp
- 染色
- 题解
- acm
categories:
- acm
---
# 题目大意：
有$n$个节点，构成一棵树，$w_i$代表i节点被选择可以延伸染色的距离为$w_i$，选择最少的节点，使得所有节点被染色。
# 解题思路：
使用树形dp进行求解。
## 状态设计
$f[u][j]$：$u$节点为根的子树已经被全部染色，且其能从$u$节点向子树外延伸的最远距离为$j$的最小花费

example：$f[u][0]$就代表子树全部染色，且不会染色子树外的节点，$f[u][2]$代表$u$子树全部染色，且能染色它父亲的父亲节点和兄弟节点。

$g[u][j]$：$u$节点为根的子树内还未完全染色，且未被染色的节点与$u$最远距离为$j$的最小花费

example：$g[u][0]$代表$u$节点还未染色，子树其余节点全部染色，$g[u][1]$代表其儿子中至少有一个节点未染色，$u$节点可能未染色，其儿子为根的子树已经全部染色
<!-- more -->
## 状态转移
### u节点未被选择

考虑$f[u][j]$在$   u$未被选择的情况下，所有的前驱状态，其必须有一个儿子$v_i$能够向子树外延伸$j+1$,即其状态为$f[v_i][j+1]$，至于其他的儿子$v_k$，在这个前提下，可选的状态有$f[v_k][0,…,j,j+1]$，$v_k$的子树完全染色，最多向外延伸$j+1$,还有$g[v_k][0，…，j-1]$，$v_i$能向外延伸$j+1$的距离，其到$v_k$的距离为$2$，也就是说$v_k$的子树可以由$v_i$的延伸染色最远距离为$j-1$，来使该子树染色完全

枚举$v_i$，方程为：

$f[u][j]=min \\{
    f[v_i][j+1]+
    \sum min\\{
        f[v_k][0,…,j,j+1],g[v_k][0，…，j-1]
    \\}
\\}
(j\geqslant 0)$

对于$g[u][j]$，必须有一个儿子$v_i$子树内存在最远距离为$j-1$的未染色节点，在这个前提下，其余的儿子$v_k$，可选的状态为$g[v_k][0，…，j-1]$（距离$j$时就不合法了），及$f[v_k][0，…，j]$（$j+1$就会导致$v_i$子树被染色）

枚举$v_i$，方程为：

$g[u][j]=min\\{
    g[v_i][j-1]+\sum min\\{
        f[v_k][0,…,j],g[v_k][0，…，j-1]
    \\}
\\}$ $(j>0)$

上面的方程，$j=0$的情况需要特殊考虑，$g[u][0]代表子树只有u$未染色，那么它的前驱状态一定是$f[v][0]，其中v是u的儿子$，方程如下：

$g[u][0]=\sum f[v][0]$

再解释一下$i$和$k$，$i$是$u$的一个儿子，上面的方程首先枚举$i$,$k$为$u$儿子中除$i$之外的

### u节点选中

若节点选中，可被更新的状态有$g[u][w_u+1,…]$和$f[u][w_u,w_u+1,…]$，但是$u$节点不选时$g[u][w_u+1,…]$一定比选中小，而对于$f[u][w_u+1,…]$,选择$u$更是多余，于是有意义的更新状态就剩下$f[u][w_u]$了

其前驱状态要满足不会有未染色点到$u$的距离大于$w_u$，也不会有向子树外延伸距离超过$w_u$的，故可选的状态有$g[v][0，…，w_u]$和$f[v][0,…,w_u]$，方程：

$f[u][w_u]=1+\sum min\\{g[v][0，…，w_u],f[v][0,…,w_u]\\}$

## 时间复杂度

设有$n$个节点，节点延伸上限为$m$，有$n*m$个状态需要更新，每次更新需要儿子的$m$个状态的最小值

时间复杂度为$O(n*m^2)$

在这道题里，$n$为$10^5$,$m$为$10^2$，复杂度不能接受

## 算法优化

在上面的转移方程中，几乎都需要求$min\\{g[v][0,…,j]\\}$和$min\\{f[v][0,…,j]\\}$，可以预处理其前缀最小，使得$gp[v][j]=min\\{g[v][0,…,j]\\}$，$fp[v][j]=min\\{f[v][0,…,j]\\}$，复杂度变为$O(n*m)$

上面的方程就变成了：

### $不选择u:$

$f[u][j]=min\\{f[v_i][j+1]+\sum min\\{fp[v_k][j+1],gp[v_k][j-1]\\} \\}$ $(j \geqslant 0)$

$g[u][j]=min\\{g[v_i][j-1]+\sum min\\{fp[v_k][j],gp[v_k][j-1]\\} \\}(j>0)$

$g[u][0]=\sum f[v][0]$

### $选择u:$

$f[u][w_u]=1+\sum min\\{gp[v][w_u],fp[v][w_u]\\}$

## 代码优化

前缀最小值的优化，已经使题目可解，但消耗了不必要的内存空间

可以通过保证$f[u][…]$和$ｇ[u][…]d$的单调性，来替代前缀最小数组

维护$f$与$g$数组，使得对于$i<j$,$f[u][i] \leqslant f[u][j]$,$g[u][i]\geqslant g[u][j]$，且$f[u][0]\geqslant g[u][0]$

这样，$g[v_k][j-1]=min\\{f[v_k][0,…,j,j+1],g[v_k][0，…，j-1]\\}$,方程变为：

### $不选择u:$

$f[u][j]=min\\{f[v_i][j+1]+\sum g[v_k][j-1] \\}(j \geqslant 0)$

$g[u][j]=\sum g[v_k][j-1] (j>0)$

$g[u][0]=\sum f[v][0]$

### $选择u:$

$f[u][w_u]=1+\sum g[v][w_u]$

## 细节处理

$f[u][j]=min\\{f[v_i][j+1]+\sum g[v_k][j-1] \\}$，这样的方程，首先求出$g[u][j]=\sum g[v_k][j-1]$,然后$f[u][j]=min\\{f[v_i][j+1]+g[u][j]-g[v_i][j-1] \\}$

# 代码

```c++
const int maxn = 100005;
int n;
int w[maxn];
int head[maxn];
int cnt;
struct Edge{
    int v,next;
}e[maxn<<1];
void init()
{
    memset(head,-1,sizeof(head));
    cnt=0;
}
void add(int u,int v)
{
    e[cnt].v=v;
    e[cnt].next=head[u];
    head[u]=cnt++;
}

int f[maxn][105],g[maxn][105];

void dfs(int u,int fa)
{
    for(int i=0;i<=100;i++)f[u][i] = n;
    cl(g[u],0);

    for(int i=head[u];i!=-1;i=e[i].next){
        int v = e[i].v;
        if(v==fa)continue;
        dfs(v,u);
        g[u][0] += f[v][0];
        for(int j=1;j<=100;j++){
            g[u][j] += g[v][j-1];
        }

    }

    f[u][w[u]] = min(f[u][w[u]],g[u][w[u]]+1);
    for(int i=head[u];i!=-1;i=e[i].next){
        int v = e[i].v;
        if(v==fa)continue;
        f[u][0] = min(f[u][0],f[v][1]+g[u][0]-f[v][0]);
        for(int j=1;j<=99;j++){
            f[u][j] = min(f[u][j],f[v][j+1]+g[u][j]-g[v][j-1]);
        }
    }
    for(int j=99;j>=0;j--){
        f[u][j] = min(f[u][j],f[u][j+1]);
    }
    g[u][0] = min(g[u][0],f[u][0]);
    for(int j=1;j<=100;j++){
        g[u][j] = min(g[u][j],g[u][j-1]);
    }

}

int main()
{
    while(scanf("%d",&n)!=EOF)
    {
        for(int i=1;i<=n;i++)scanf("%d",&w[i]);
        init();
        int x,y;
        for(int i=0;i<n-1;i++){
            scanf("%d%d",&x,&y);
            add(x,y);add(y,x);
        }

        dfs(1,0);
        printf("%d\n",f[1][0]);
    }
    return 0;
}
```

# 相关题目

这个题目还是相当复杂的树形dp染色问题，推荐几道弱一点的题目

[uva 11307 Alternative Arborescence
](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=2282)

[uva 1218 Perfect Service ](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=3659)

[hdu 4340 Capturing a country](http://acm.hdu.edu.cn/showproblem.php?pid=4340)
