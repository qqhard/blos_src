title: bandit learning
date: 2016-07-08 15:15:51
tags:
- 算法
- 机器学习
categories:
- 机器学习
---

# bandit learning问题描述

在游戏厅，有多臂老虎机游戏，但是摇动老虎机每个arm，获得的收益概率不同。在我们不知道每个arm收益的前提下，我们想在给定次数内通过转动arm，获取最大的收益。

<!-- more -->

# bandit learning应用场景

1 在企业中，算法工程师关于一个问题上线很多模型，想知道哪个模型更好。

2 在广告展示中，需要知道给用户展示哪个广告，才能获得最大化收益？每次都挑当前效果最好的，还是每次都选取新广告。

类似的还有很多，关于选择的问题都可以简化成多臂老虎机问题。

bandit learning是用于解决多臂老虎机问题的算法，在计算广告和推荐系统方面应用很多，又被称为ee问题，即Exploit-Explore问题，具体解释如下：

exploit：英语解释是开发利用，根据我们现在获得的信息，我们可以知道每个arm在之前的实验中的表现，那么我们利用这个信息，接下来摇动之前表现好的arm。这种思路下认为获得的收益大。

explore：英语解释是探索。即在问题中每个arm的收益是服从概率分布的，之前摇动获得的收益高的arm并不一定是最优的arm，所以我们还应该继续探索，通过多次摇动每个arm来寻找最优arm。

# bandit learning算法介绍

bandit learning目的是解决每次摇臂的时候选择哪个arm，这篇文章中我们假设每个arm的收益背后服从一个高斯分布。我们先用下面几行代码随机生成每个arm的高斯分布。

```python
C = 10.0
arm_num = 8#arm的个数
arm_args = []#存每个arm具体高斯分布
ALL_TIMES = 1000#共摇臂次数

for i in range(arm_num):
    arm_args.append((random()*C,random()*C))
```

bandit learning具体算法有很多种，下文给出几种经典方法的介绍和代码。

## 完全随机方法

即每次对arm的选择完全不考虑先验知识，而是从所有arm中随机选择，代码如下：

```python
def try_arm_dive_times(all_times):
    bandit = 0.0
    for i in range(arm_num):
        arm_times = all_times / arm_num
        if i <all_times % arm_num:
            arm_times +=1
        a,b = arm_args[i]
        for j in range(arm_times):
            bandit += gauss(a,b)
    return bandit
```

## 朴素随机方法

根据上面的方法，每次都是随机乱猜固然是不可取的，这种方法相对上种有了一些改进，首先对每个arm尝试一次，然后接下来一直选择表现最好的arm摇动。代码如下：

```python
def try_arm_once_and_run_best(all_times):
    bandit = 0.0
    best_pos = 0
    try_arrs = []
    all_times = all_times - arm_num
    for i in range(arm_num):
        try_arrs.append(gauss(arm_args[i][0],arm_args[i][1]))
    for i in range(arm_num):
        if try_arrs[i]>try_arrs[best_pos]:
            best_pos = i
    a,b = arm_args[best_pos]
    for i in range(all_times):
        bandit += gauss(a,b)
    return bandit
```

## e-greedy算法

这个算法考虑到exploit和explore的分配，给定0-1之间的一个值e，代码每次生成一个0-1之间的随机数，如果该值小于e,则探索，在所有arm中随机选择一个，否则利用先验知识，选择所有arm中当前平均收益最大的arm，通过调整e的值可以调整探索和开发利用的比例，越接近0，则程序越保守，使用当前表现好的arm，通常e值取值较小。代码如下：

```python
def e_greedy(all_times,e):
    bandit = 0.0
    arms_bandit = [0 for i in range(arm_num)]
    arms_count = [0 for i in range(arm_num)]
    for i in range(all_times):
        k = 0
        if random()<e:
            k = randint(0,arm_num-1)
        else:
            for j in range(arm_num):
                if arms_bandit[j] > arms_bandit[k]:
                    k = j
        v = gauss(arm_args[k][0],arm_args[k][1])
        bandit = bandit+v
        arms_bandit[k] = (arms_bandit[k]*arms_count[k]+v)/(arms_count[k]+1)
        arms_count[k] = arms_count[k]+1
    return bandit
```

## softmax算法

softmax方法是在e_greedy方法上做了改进，上一种算法每次以固定概率进行探索，探索时每个臂被选中的概率相同，是完全随机的。但是softmax算法考虑到不同收益的臂被选中的概率应该不同，根据先验知识之前摇臂中平均收益越大的臂被选中的概率越大。因此softmax算法每次对每个arm选择的概率取决与该arm之前的平均收益。代码如下：

```python
def soft_max(all_times,r):
    bandit = 0.0
    arms_bandit = [0 for i in range(arm_num)]
    arms_count = [0 for i in range(arm_num)]
    probs = [0 for i in range(arm_num)]
    for i in range(all_times):
        prob_sum = 0.0
        for j in range(arm_num):
            probs[j] = math.pow(math.e,arms_bandit[j]/r)
            prob_sum += probs[j]
        k = 0
        sel_random = random() * prob_sum
        sel_sum = 0.0
        for j in range(arm_num):
            sel_sum += probs[j]
            if sel_sum > sel_random:
                k = j
                break
        v = gauss(arm_args[k][0],arm_args[k][1])
        bandit += v
        arms_bandit[k] = (arms_bandit[k]*arms_count[k]+v)/(arms_count[k]+1)
        arms_count[k] = arms_count[k]+1
    return bandit
```

## ucb1算法

上面两种算法都需要进行参数配置，但是ucb1没有先验知识和条件。该算法首先对每个arm尝试一次，然后接下来每次选择下公式值最大的臂。
$\overline {X_j}(t) +\sqrt {\dfrac {2\ln t} {T_j}}$
其中前部分是这个臂到目前的收益均值，后一部分是均值的标准差，t是目前的试验次数，$T_j$是这个臂被试次数。
这个公式反映：均值越大，标准差越小，被选中的概率会越来越大，起到了exploit的作用；同时哪些被选次数较少的臂也会得到试验机会，起到了explore的作用。代码如下：

```python
def ucb1(all_times):
    bandit = 0.0
    arms_bandit = [0 for i in range(arm_num)]
    arms_count = [0 for i in range(arm_num)]
    for i in range(arm_num):
        v = gauss(arm_args[i][0],arm_args[i][1])
        arms_bandit[i] = v
        bandit += v
        arms_count[i] = 1
        all_times -=1
    for i in range(1,all_times+1):
        k = 0
        m = -100000000
        for j in range(arm_num):
            tmp_ucb = arms_bandit[j] + math.sqrt(2.0*math.log(i+arm_num)/arms_count[j])
            if tmp_ucb>m:
                k = j
                m = tmp_ucb
        v = gauss(arm_args[k][0],arm_args[k][1])
        bandit = bandit + v
        arms_bandit[k] = (arms_bandit[k]*arms_bandit[k]+v)/(arms_count[k]+1)
        arms_count[k] = arms_count[k]+1
    return bandit
```

## bandit learning算法效果对比

算法效果对比之前首先明确对比的指标，在多臂问题中使用累计遗憾定义，累计遗憾计算如下：

$R{T}=\sum {i=1}^{T}W{opt}-W{g}\left( t\right)$

其中$W\_{opt}$指每次选择最优的时候的收益，Wg(i)指第i次选择被选中的臂的期望收益。对于不用的bandit learning 算法，可以通过对比累积遗憾来衡量算法效果。

上述几个算法对比后发现，完全随机<朴素随机<e-greedy<softmax<ucb1
