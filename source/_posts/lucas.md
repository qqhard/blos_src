title: lucas定理
date: 2015-09-15 09:33:23
tags:
- 数论
categories:
- acm
---
### 递归：
	ll lucas(ll a,ll b,ll p)
	{
	    if(a%p<b%p)return 0;
	    if(b==0)return 1;
	    return lucas(a/p,b/p,p)*Com(a%p,b%p,p)%p;
	}
### 非递归：
	ll lucas(ll a,ll b,ll p)
	{
	    ll ret=1;
	    while(a&&b){
	        if(a%p<b%p)return 0;
	        ret=ret*Com(a%p,b%p,p)%p;
	        a/=p;
	        b/=p;
	    }
	    return ret;
	}
