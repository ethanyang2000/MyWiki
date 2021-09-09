# Monte Carlo

## Motivation
在进行大量的求和或者积分的运算时，很难精确求解。因此常用采样的方法进行近似计算以减小开销。

## Sampling
进行采样计算时，可以把$f(x)$的求和或者积分看作是$f(x)$在分布$p(x)$上的期望.  
假设我们数据的概率分布是 $p(x)$ ，我们要求在这一分布上函数 $f(x)$ 的期望。蒙特卡洛方法即是通过从$p(x)$中采样$n$个样本，并用经验平均值(empirical average)来近似.$$\hat {s_n} = \frac 1n \sum_{i=1}^n f(x^{(i)})$$  
以上近似的理论基础有两点：
1. $\hat s_n$是期望的无偏估计。且根据大数定理，如果样本是独立同分布，则${lim}_{n \to \infty} \hat s_n = s$
2. 另一方面， 单项方差有界时，$\hat s_n$的方差与采样量$n$是成反比的，当采样量足够多时，我们可以很好的近似真实期望。$$Var[\hat s_n] = \frac 1n^2 \sum_{i=1}^n Var[f(x)] = \frac {Var[f(x)]}n$$

## importance sampling
观察求和形式,需要对式子进行分解 $p(x)f(x)$ 。我们可以发现如何选取 $p(x)$ 和$f(x)$ 并不是唯一的，即我们总可以将其分解为 $p(x)f(x) = q(x)\frac {p(x)f(x)}q(x)$ 的形式而不改变求和结果，我们可以将 $q(x)$ 当做新的 $p(x)$ 而 $\frac {p(x)f(x)}q(x)$ 当做新的$f(x)$ 。因此，我们可以从分布$q(x)$ 中取样，并利用$$\hat {s_q} = \frac 1n \sum_{i=1,x^{(i)}~q}^n \frac {p(x^{(i)})f(x^{(i)})}{q(x^{(i)})}$$求 $f(x)$ 的期望，这一方法被称作**重要采样importance sampling**。  
估计的期望与$q(x)$无关，但方差对$q(x)$敏感。
$$E_q[\hat s_q] = E_p[\hat s_p] = s$$

$$ Var[\hat s_q] = \frac {Var[\frac {p(x)f(x)}q(x)]}n $$
方差若取最小，q需要满足(Z为归一化常数)
$$q^*(x) = \frac {p(x)|f(x)|}Z$$
好的重要性采样分布会把更多的权重放在被积函数较大的地方。当$q^*$已知的时候，只需要采一个样本（需要计算原求和，我们又回到最初的问题，实际上无法采用）。从期望看任意q都可行，从方差看要尽量靠近
$q^*$  
在高维分布中q很难选择，很容易产生过估计或欠估计。

## MCMC
在基于能量的模型中，不同状态的采样相互依赖。对于有向图可以用ancestral sampling的方法采样，即按照拓扑顺序。而对于无向图由于没法进行拓扑排序不能用ancestral sampling，解决方法之一是利用马尔可夫链蒙特卡罗方法(Markov Chain Monte Carlo， 简称MCMC)。  
Markov Chain的核心思想是我们最初可以随机的选取初始状态，再不断的更新这一状态，最终使其分布近似于真实分布$p(x)$

[to be continued](https://zhuanlan.zhihu.com/p/48481980)