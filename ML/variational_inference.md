# 变分推断（variational inference）
## Motivation
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/BI.jpg?raw=true">  
贝叶斯模型的训练问题：实际计算中积分部分（Evidence）的计算往往是intractable的。这种情况下，我们有两种方法去进行处理，一种是变分推断（Variational Inference），另一种是马尔科夫蒙特卡洛（MCMC）.  
变分推断的主要思路是在一个分布族$Q$中找到后验分布的近似：
$$p(\theta|x) \approx q(\theta) \in Q$$
可以利用下述约束条件：
$$F(q):=KL(q(\theta)||p(\theta|x)) \to \min_{q(\theta) \in Q}$$

## Methods
变分推断的主要思想就是寻找一个$q(\theta)$去近似后验$p(\theta|x)$，那么引出的问题就是如何衡量近似的好坏，在变分推断中我们希望$p$和$q$两个分布尽可能接近，那么如何度量两个分布的距离？这里引入KL散度这种度量方式：Kullback-Leibler divergence
$$KL(q(\theta)||p(\theta|x)) = \int q(\theta)\log \frac{q(\theta)}{p(\theta|x)}d\theta$$
其具有非负性和不对称性。等号成立当且仅当同分布。  
然而在KL散度的表达式里还是有后验分布，这个是不能被直接去计算的，因此，变分推断将一个推断问题转化为优化问题，即通过最小化KL散度使得两个分布尽可能接近。  
那么如何去对一个分布进行优化呢？我们发现，$\log p(x)$可以进行如下转化：
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VI1.jpg?raw=true">  
得到的分解结果正好有两项，一项是我们需要的KL散度（红色），另外一项是 “分解剩下来的东西”，一般称之为变分证据下界（Evidence Lower Bound，ELBO）。ELBO顾名思义，由两个部分组成，即Evidence和Lower Bound：
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VI6.jpg?raw=true">  
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VI4.jpg?raw=true">  
仔细看上面的式子，$\log p(x)$不依赖于$q$，在优化问题中是固定的(导数为零)，后面的KL散度和ELBO依赖于$q$。因此，最小化KL散度等价于最大化Evidence Lower Bound。
$$L(q(\theta)) = \int q(\theta) \log \frac{p(x,\theta)}{q(\theta)}d\theta \to \max_{q(\theta)\in Q}$$

## assumption
前面将分布的近似问题转化为了优化问题，并证明了优化问题本质上就是在做最大化ELBO这件事情，就引入了下一个问题：如何去最大化ELBO。为此，我们需要先对我们使用的近似后验$q(\theta)$进行形式上的假设，常见的假设有如下两种：
1. 平均场近似（mean field approximation）： factorized family
根据乘法法则：
$$q(\theta) = \prod_{j=1}^m q_j(\theta_j|\theta_{\lt j})$$
mean field为最为简洁的假设，即各个参数$\theta_i$是相互独立的，这里我们使用块坐标上升法(Block Coordinate Assent) 进行优化：
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VI2.jpg?raw=true">  
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VI3.jpg?raw=true">  
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/MAVEN2.jpg?raw=true">  
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VI7.jpg?raw=true">  
具体算法为：
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VI.jpg?raw=true">  

使用这种方法时，要注意需要满足的条件（以及如何检验这种条件是否满足）：
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VI5.jpg?raw=true">  

2. 参数化近似（parametric approximation）： parametric family
在近似分布$q(\theta)$的选取中，我们需要先对q进行一系列的假设。展开来讲，我们需要寻找一个固定的分布族。当然我们可以对分布进行较为简单的假设（如上面的Mean Field），产生的问题就是可能不能捕捉到数据中全部的信息，然而过于复杂的假设又会使得训练的过程变得困难。
这种方法将分布参数化：
$$q(\theta) = q(\theta|\lambda)$$
其中$\lambda$是参数。这样变分推断可以转化为参数优化问题：
$$L(q(\theta|\lambda)) = \int q(\theta|\lambda)\log \frac{p(x,\theta)}{q(\theta|\lambda)}d\theta \to \max_\lambda$$
