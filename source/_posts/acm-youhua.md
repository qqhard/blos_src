title: acm-常见优化
date: 2016-06-04 10:57:25
tags:
- acm
- 优化
categories:
- acm
---

acm中，dp一般很少裸着出现，常常伴随着各种各样的优化，例如，数据结构在累加和上的优化，斜率优化，四边形优化，单调队列优化。

<!-- more -->

# 斜率优化
```c++
int s[10005];
int dp[2][10005];
int n,m;
int now,pre;
int getY(int j,int k)
{
    return dp[pre][j]+s[j+1]*s[j+1]-dp[pre][k]-s[k+1]*s[k+1];
}
int getX(int j,int k)
{
    return (s[j+1]-s[k+1])*2;
}
struct Q{
    int head,tail;
    int q[10005];
    void init()
    {
        head=tail=0;
    }
    void push(int i){
      	while(head+1<tail&&getY(i,q[tail-1])*getX(q[tail-1],q[tail-2])<=getY(q[tail-1],q[tail-2])*getX(i,q[tail-1]))tail--;
        q[tail++]=i;
    }
    int getDp(int i)  {
 	      while(head+1<tail&&getY(q[head+1],q[head])<=getX(q[head+1],q[head])*s[i])head++;
        return dp[pre][q[head]]+(s[i]-s[q[head]+1])*(s[i]-s[q[head]+1]);
    }
}q;

int main()
{
    int T;
    scanf("%d",&T);
    int Case=1;
    while(T--)
    {
        scanf("%d%d",&n,&m);
        for(int i=1;i<=n;i++){
            scanf("%d",&s[i]);
        }
        sort(s+1,s+1+n);
        dp[0][0]=dp[1][0]=0;
        for(int i=1;i<=n;i++)dp[0][i]=(s[i]-s[1])*(s[i]-s[1]);
        now=0;pre=1;
        for(int k=0;k<m-1;k++){
            now=now^1;pre=pre^1;
            q.init();
            q.push(0);
            for(int i=1;i<=n;i++){
                dp[now][i]=q.getDp(i);
                q.push(i);
            }
        }
        printf("Case %d: %d\n",Case++,dp[now][n]);
    }
    return 0;
}
```
# 状态压缩一个重要的优化
http://acm.hdu.edu.cn/showproblem.php?pid=3920
题目大意：有n*2个敌人，你每次可以用激光枪打两个敌人，代价为你的位置到第一个敌人的直线距离+第一个敌人到第二个敌人的直线距离，一共开n抢，问最小的代价。
解题思路：状态压缩，某一位为1表示敌人已经消灭，为0表示还没有消灭，方程为dp[s]=min{dp[s^(1<<i)^(1<<j)]+cost(i,j)},
i和j位为0，cost(i,j)表示i到j的最小花费
如果正推，会出现许多无效状态，大概有50w，导致了tle，所以使用记忆化搜索
但是枚举组合复杂度仍然是n*n*2^n，n最大是20，超时是肯定的了
假设n=4，我们画一下记忆搜的搜索树

颜色相同的状态，对他们的父亲状态的最优解造成的效果是相同的，我们思考一下方程，父状态的最优解是其所有组合的子状态中最小的，但是在枚举组合的过程中，有了先后顺序，而这先后顺序并不影响最优解，那么就要想法去掉这些状态，抛开方程，我们想，对于父状态s，他的子状态应该是其中两位1变为0，那么对于状态最靠右的一位1来讲，这是迟早要选取的点，早选晚选都是一样，我们的最优的决策不取决于在搜索的哪一层选取这个点，而在于在这一个点该和其余的哪个点组合，所以选取第一个点，枚举其余点，复杂度变为n*2^n，就能过了
画一下搜索树：

代码：
```c++
typedef double ff;
ff sx,sy;
ff dp[1050000];
ff MIN[20][20];
struct Point{
    ff x,y;
}p[22];
int n;
double dfs(int state)
{
    if(dp[state]!=-1)return dp[state];
    dp[state]=1<<30;
    for(int i=0;i<n;i++){
        if((state>>i)&1){
            for(int j=i+1;j<n;j++){
                if((state>>j)&1){
                    dp[state]=min(dp[state],dfs(state^(1<<i)^(1<<j))+MIN[i][j]);
                }
            }
            break;
        }
    }
    return dp[state];
}
int main()
{
    int T;
    scanf("%d",&T);
    int Case=1;
    while(T--)
    {
        scanf("%lf%lf",&sx,&sy);
        scanf("%d",&n);
        n=n*2;
        for(int i=0;i<n;i++){
            scanf("%lf%lf",&p[i].x,&p[i].y);
        }
         for(int i=0;i<n;i++){
            for(int j=i+1;j<n;j++){
                MIN[i][j]=sqrt(min((sx-p[j].x)*(sx-p[j].x)+(sy-p[j].y)*(sy-p[j].y),(sx-p[i].x)*(sx-p[i].x)+(sy-p[i].y)*(sy-p[i].y)));
                MIN[i][j]+=sqrt((p[i].x-p[j].x)*(p[i].x-p[j].x)+(p[i].y-p[j].y)*(p[i].y-p[j].y));
            }
        }
        for(int i=0;i<(1<<n);i++)dp[i]=-1;
        dp[0]=0;
        printf("Case #%d: %.2lf\n",Case++,dfs((1<<n)-1));
    }
    return 0;
}
```
