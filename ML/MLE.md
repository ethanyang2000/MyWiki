# 极大似然估计
## Background
极大似然估计是参数估计的一种方法。使用的前提是训练样本的分布能代表样本的真实分布。每个样本集中的样本都是所谓独立同分布的随机变量 (iid)，且有充分的训练样本。  
极大似然估计提供了一种给定观察数据来评估模型参数的方法，即：“模型已定，参数未知”。

## Methods
记一类iid的样本集：
$$D=\{x_1, x_2,\cdots ,x_N\}$$
联合概率密度函数$p(D|\theta)$ 称为相对于样本集$D$的$\theta$的似然函数。由于样本集已知，可以看作是关于参数$\theta$的函数。即在概率密度函数的参数是$\theta$时，得到这组样本的概率
$$l(\theta) = p(D|\theta) = p(x_1, x_2,\cdots ,x_N|\theta) = \prod_{i=1}^{N}p(x_i|\theta)$$
如果$\hat \theta$是参数空间中能使似然函数最大的$θ$值，则$\hat \theta$应该是“最可能”的参数值，那么$\hat \theta$就是$θ$的极大似然估计量。它是样本集的函数，记作：
$$\hat \theta = d(D)$$
最大值可以通过求导得到。  
实际计算中为了方便求导，常使用对数似然函数$H(\theta) = \ln l(\theta)$
$$\hat \theta = arg \max_\theta H(\theta) = arg \max_\theta \ln l(\theta) = arg \max_\theta \sum_{i=1}^N \ln p(x_i|\theta)$$

## Another view
抽样过程中分布密度大的样本容易被抽到，因此认为抽到的样本都是常出现的。即样本使得联合概率分布最大，因此需要找到能最大化联合概率分布（似然函数）的参数。  
MSE和Cross Entropy都可以从极大似然估计的角度来看待。