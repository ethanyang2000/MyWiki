# QTRAN
## Introduction
在value-based MARL算法中，很重要的一种是对联合action-value-function进行分解。其中的代表算法是QMIX和VDN
+ *VDN*:将联合action-value-function分解为个体价值函数的加和
+ *QMIX*:将个体价值函数的加性关系扩展为单调关系，由此可以cover更多的行为。  
但上述方法仍有structural constraints。  
如果个体价值函数给出的个体行为的联合等价于联合价值函数给出的联合行为，我们称task是可分解的。需要注意的是加性分解和单调性只是该问题的充分条件，但不是必要的。  
## Contributions
+ 可以分解任何可分解的task，没有约束条件
+ 将原始联合价值函数映射为新的易分解的价值函数，并保证二者有相同的optimal action
+ QTRAN

## Background
### Dec-POMDP & CTDE
### IGM & Factorizable
定义IGM（Individual-Global-Max）：
其中$Q_{jt}：\Tau^NN X U^N \to R$是联合action-value-function，$\tau \in \Tau^N$是联合trajectories，假设存在个体action-value-function $[Q_i:\Tau X U \to R]_{i=1}^N$ 满足上述条件，称$[Q_i]$ 在 $\tau$ 下对 $Q_{jt}$ 满足IGM，即 $Q_{jt}(\tau,u)$ 被 $[Q_i(\tau_i,u_i)]$ 分解。若其在任何trajectory下都可分解，则称其可分解。

### VDN & QMIX
VDN和QMIX分别具有下面的约束条件，其都是IGM的充分条件。

## QTRAN
QTRAN可以分解任何可分解的task，核心思想是将原始的joint action-value-func映射到一个新的函数，并且二者有相同的optimal action

### Conditions for factor functions
$\hat u_i$ 代表local optimal action $\argmax_{u_i} Q_i(\tau_i,u_i)$ 且 $\hat u = [\hat u_i]_{i=1}^N$  

上述条件在仿射变换下仍然必要  


我们定义个体价值函数的线性和：
$$Q'_{jt}(\tau, u) = \sum^N_{i=1} Q_i(\tau_i, u_i)$$
$Q'_{jt}$ 记为 transformed joint-action-value-function。由于上述加式满足IGM条件，因此保证了分解性。同时由于 $Q'_{jt}$ 保证了optimal action不变，因此 $[Q_i]$ 也是原函数的分解。  
函数 $V_{jt}(\tau)$ 修正了 $Q_{jt}$ 和个体价值函数和 $[Q_i]$ 的差值。这个差值来源于agent的部分观察。如果agent是完全观察的， $V_{jt}$ 可以定义为零。

## Methods
### architecture

包含三个不同的estimator：
+ 每个agent individual action-value-function for $Q_i$
+ 联合action-value-function for $Q_{jt}$
+ state-value-function for $V_{jt}$
网络使用中心化的方式训练，在执行阶段每个agent根据 $Q_i$ 来选择动作。

1. *Individual action-value networks*： 输入是每个agent自己的trajectory，产生一些q-value $Q_i(\tau_i , \cdot)$
2. *Joint action-value network*: 输入选择的动作，产生输入动作的q-value $Q_{jt}$ .网络的使用有以下的要点：
    + 使用子网络选择的最优动作来更新此网络。因为子网络各自的$\argmax$ 操作对agent数目是线性的。
    + 此网络和individual network的底层共享参数。具体来说是将hidden features线性加和。
    $$\sum_i h_{Q,i}(\tau_i,u_i)$$
    $$of \:\:\: h_i(\tau_i,u_i) = [h_{Q,i}(\tau_i,u_i),h_{V,i}(\tau_i)] from individual networks$$
3. *state-value-network*
    用来计算state-value标量。没有这部分，部分观测性就会限制 $Q'_{jt}$ 的表征复杂性。  
    由于这里计算不用actions，因此这个网络对选择action没有作用，而是用来计算loss。  
    我们在这里同样使用了参数共享，用组合的hidden features $\sum_i h_{V,i}(\tau_i)$ 作为输入。

### Loss function
centralized training阶段有两个目标：
+ 使得 joint action value function $Q_{jt}$ 尽可能估计真实的action-value function
+ transformed action-value function $Q'_{jt}$ 需要追踪 $Q_{jt}$ 以保持optimal action一致。
具体使用了类似DQN的算法，维护了一个target network和replay buffer，loss如下：
$$L(\tau, u, r, \tau ' : \theta) = L_{td} + \lambda_{opt}L_{opt} + \lambda_{nopt}L_{nopt}$$

+ r 是action u得到的reward
+ $L_{td}$ 是逼近实际action-value function的loss，即minimize TD-error。
+ $L_{opt}$ 和 $L_{nopt}$ 是根据上述条件分解 $Q_{jt}$ 产生的loss。前者保证optimal local action满足条件a，后者保证每一步选择的action满足条件 b。  
但是验证a是否满足需要进行大量的采样，因为optimal actions在训练时很少会被采用。考虑到我们希望学到 $Q'_{jt}$ 和 $V_{jt}$ 来分解 $Q_{jt}$，我们可以在学习 $L_{opt}$ 和 $L_{nopt}$ 时固定 $Q_{jt}$ 来稳定训练过程。  
记 $\hat Q_{jt}$ 为固定的 $Q_{jt}$。loss变成如下形式:
$$
