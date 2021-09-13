# VMAPD(Variational Multi-Agent Policy Diversification)
## background
很多MARL算法得到的都是单一的最优解，但很多问题有不止一个最优解，我们希望得到多样化的行为。  
本文提出了一种CTDE的MARL方法，VMAPD。我们将MARL控制问题用概率图模型来表示，并加入了共享的潜变量来揭示trajectory的多样性。然后通过变分推断，我们可以得到VMAPD算法。  
VMAPD算法在共享trajectory上加入了ELIBO，并通过策略迭代最大化ELIBO。实现时我们可以在CT阶段加入一个pseudo reward。

## contributions
+ 将diverse MARL问题转换为联合概率图模型并将一个ELIBO作为优化目标。 The information bottleneck term in the ELBO can prevent the diversity from degenerating to the behavior of a single agent.
+ 使用拉格朗日乘子法引入了改良的ELIBO，以将reward maximization和policy diversification解绑。并且使用了PI控制的原则动态调整拉格朗日乘子。  
+ 提出了VMAPD，只需要调整潜变量就能得到多样的行为。