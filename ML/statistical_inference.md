# 统计推断（statistical inference）

## Problem
给定一组从分布$P(x|\theta)$得到的iid的样本$X$，估计分布的参数$\theta$。

## Frequentist framework
使用极大似然估计MLE。
$$\theta_{ML} = arg \max p(x|\theta) = arg\max \prod_{i=1}^n p(x_i|\theta) = arg\max \sum_{i=1}^n \log p(x_i|\theta)$$

## Bayesian framework
将$\theta$的不确定性编码在先验分布$p(\theta)$中
$$p(\theta|X) = \frac {\prod_{i=1}^n p(x_i|\theta)p(\theta)}{\int \prod_{i=1}^n p(x_i|\theta)p(\theta)d\theta}$$
频率学派可以看作贝叶斯学派的特殊形式：
$$\lim_{\frac nd \to \infty} p(\theta|x_1,x_2,\cdot,x_n) = \delta(\theta-\theta_{ML})$$

使用贝叶斯方法有诸多好处：首先我们可以将先验知识很自然地融入到概率建模之中；其次，先验可以充当正则化的角色；并且相比频率学派中的点估计，贝叶斯估计的后验拥有更多估计量Uncertainty的信息。

## Generative v.s Discriminative
### Discriminative
+ models$p(y,\theta|x)$,需要x作为输入，不能生成新样本。
+ 常假设$\theta$先验分布与$x$无关，$p(y,\theta|x) = p(y|x,\theta)p(\theta)$

### Generative
+ 对联合分布建模，$p(x,y,\theta) = p(x,y|\theta)p(\theta)$，可以生成新样本
+ observed space($x$)比hidden space($\theta$)复杂得多，难以得到

## 精确推断 v.s 近似推断
[To be Continued]