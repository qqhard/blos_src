title: hihocoder11D 树上路径相交
date: 2015-09-18 22:26:14
tags:
- 题解
- lca
categories:
- acm
---
### 题目

在一个n个节点的树上，给出p个路径，问有多少对路径可以相交?

### 思路

在一个树上，若两个路径相交，必其中一个路径的lca落在另一个的路径上，可以分为两种情况，1，两个路径的lca在同一个点，2，其中一个的lca在另一个路径的非lca点上。

对此，我们只要统计每个点被所有路径(抛去lca点)覆盖的次数d[i],被路径的lca点覆盖的次数sum[i],然后累加d[i]*sum[i] + sum[i]*(sum[i]-1)/2就是答案。

Lca用tarjan离线来求，d[i]用打标记+dfs来求，具体为给路径端点d[a]++,d[b]++,d[lca(a,b)]-=2,一趟dfs做d[u]+=d[v]，v是u的子节点。
<!--more-->

### 代码

```
typedef unsigned long long ull;

typedef long long ll;

const int maxn=100005;

int head[maxn];

int ask[maxn];

struct Edge{

    int v,next,id;

}e[maxn<<2];

int cnt;

int father[maxn];

bool vis[maxn];

int lca[maxn];

int d[maxn];

int sum[maxn];

int n,p;

void add1(int u,int v)

{

    e[cnt].v=v;

    e[cnt].next=head[u];

    head[u]=cnt++;

}

void add2(int u,int v,int id)

{

    e[cnt].v=v;

    e[cnt].id=id;

    e[cnt].next=ask[u];

    ask[u]=cnt++;

}

void init()

{

    memset(head,-1,sizeof(head));

    memset(ask,-1,sizeof(ask));

    cnt=0;

}

int Find(int x)

{

    if(x==father[x])return x;

    return father[x]=Find(father[x]);

}

void LCA(int u,int fa)

{

    vis[u]=true;

    father[u]=u;

    for(int i=ask[u];i!=-1;i=e[i].next){

        int v=e[i].v;

        if(vis[v])lca[e[i].id]=Find(v);

    }

    for(int i=head[u];i!=-1;i=e[i].next){

        int v=e[i].v;

        if(v==fa)continue;

        LCA(v,u);

        father[v]=u;

    }

}

void dfs(int u,int fa)

{

    for(int i=head[u];i!=-1;i=e[i].next){

        int v=e[i].v;

        if(v==fa)continue;

        dfs(v,u);

        d[u]+=d[v];

    }

}

int main()

{

    scanf("%d%d",&n,&p);

    init();

    int a,b;

    for(int i=0;i<n-1;i++){

        scanf("%d%d",&a,&b);

        add1(a,b);add1(b,a);

    }

    for(int i=0;i<p;i++){

        scanf("%d%d",&a,&b);

        d[a]++;d[b]++;

        add2(a,b,i);add2(b,a,i);

    }

    LCA(1,0);

    for(int i=0;i<p;i++){

        d[lca[i]]-=2;

        sum[lca[i]]++;

        //printf("%d\n",lca[i]);

    }

    dfs(1,0);

    ll ans=0;

    for(int i=1;i<=n;i++){

        // printf("%d %d\n",d[i],sum[i]);

        ans+=(ll)d[i]*sum[i]+(ll)(sum[i]-1)*sum[i]/2;

    }

    printf("%lld\n",ans);

    return 0;

}

```

