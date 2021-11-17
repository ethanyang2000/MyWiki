# [Trajectory diversity for zero-shot coordination](http://proceedings.mlr.press/v139/lupu21a/lupu21a.pdf)

## Introduction
这里研究的是 zero-shot coordination(ZSC)，即agents需要独立产生用于合作游戏的策略，这些策略能和训练过程中没见过的partner合作( agents must independently produce strategies for a collaborative game that are compatible with novel partners not seen during training)。我们以policy diversity为工具，提高ZSC中的cross-play score(XP)。  
解决这个问题的第一个要点就是diversity。Self-play agents在训练时使用了自己的trajectory distribution，因此很难进行ZSC。  
之前有一种Other-play(OP)算法，rely on arbitrarily breaking the symmetries。但如果symmetries are not known a priori or if they are absent from the game,OP就会退化为SP。例如每个方向的转移概率有轻微不同，或者终点的reward存在方差。
为了解决这个问题，我们可以得到一个diverse population，然后训练一个best response。为了达成目标，我们提出了 Trajectory Diversity(TrajeDi)，一个可微的objective，目标是产生diverse RL policies。TrajeDi是Jensen-Shannon Divergence的一个推广，而且适合于部分观测的MARL环境。
contributions:
+ 说明diversity是ZSC的一个重要话题，并提出 population-based approach。
+ 提出TrajeDi

## Related work
有很多diversity的方法，但是都需要reward是可分解的。如果是single reward source，例如Hanabi，就是ill-defined。  
在Hierarchical RL领域也有diversity的研究
过去的工作主要将diversity定义为action level $\pi(a|s)$，作为state distribution$(P(s|\pi))$的一个函数。或者是state-action distribution$P(s,a|\pi)$。但由于MARL的非马尔可夫性，这些并不适用。因此我们提出了TrajeDi，第一个trajectory-based diversity objective($P(\tau|\pi)$)
在imitation learning，有些人同样基于 Jensen-Shannon divergence进行研究，但是他们的目标是minimize而不是maximize。另一个工作是PoWER，使用了trajectory-based KL divergence objective。
这里的方法和Fictitious Self-play也有共同之处。即对于一个policy过去行为的snapshot的集合训练一个BR。我们这里只考虑fully cooperative games。
另外 Other-play利用了domain knowledge，即game symmetries。但symmetries并不总是存在或是可知，因此我们rely on structure of the policy space。

## background
### Dec-POMDP
$$\cdots$$
TrajeDi的核心就是 tree $\tau$ of all possible trajectories.在joint-policy $\pi$ 的条件下trajectory $\tau = (o_0,a_0,\cdots,a_{T-1},o_{T})$ 的概率为
$$P(\tau|\pi) = \epsilon(\tau)\pi(\tau),\  where\  \epsilon(\tau):=P(s_0)\prod_{t=0}^{T-1}P(s_{t+1}|a_t,s_t)$$
这一项表达了环境的dynamics 
$$\pi(\tau):=\prod_{t=0}^{T-1}\pi(a_t|\tau_t)$$
这一项代表了joint-action probabilities。  
一个joint-policy的行为可以被distribution over trajectories表示。
### policy populations
在一个task上训练n个不同的joint-policies，我们可以定义average trajectory probability
$$P(\tau|\hat \pi) = \epsilon(\tau)\hat \pi(\tau)\  where\  \hat \pi(\tau) := \sum_{i=1}^n\frac 1n \pi_i(\tau)$$
这一项代表trajectory-wise average policy。  
同样地，令$\widetilde{\pi}$ 作为action-wise average policy，那么对于所有 $(\tau_t, a)$ pair，有
$$\widetilde{\pi}(a|\tau_t) := \sum_{i=0}^n \frac 1n \pi_i(a|\tau_t)$$
这两个定义是相似的，但 $\hat \pi(\tau) \ne \prod_{t=0}^{T-1} \widetilde{\pi}(a_t|\tau_t)$
### ZSC
在ZSC framework里面，两个player需要各自独立训练一个joint-policy。然后各自配对在cross-play中验证。
$\pi_1 \And \pi_2$ 表示两个joint-policy。
$$J_{XP}(\pi_1,\pi_2) = \frac 12 (J(\pi_1^1,\pi_2^2)+J(\pi_2^1,\pi_1^2))$$
重要的一点是，agent在训练过程中不能观察到彼此的joint-policy。
### Other-play
OP探索了可知的symmetries。给定symmetries，OP在训练过程中使用了asymmetric domain randomization，具体来说是每个episode给每个agent不同的permutation of environment $\phi(S,O,A)$。这个方法和我们的并不冲突。  
我们的方法、OP、ZSC framework可以应用到任何agent数量的游戏。为了简化我们只考虑两个。

## Methods
### population-based training setup
假设一个含有n-policies的，用SP训练的，并尽量最大化diversity的population $(\pi_1,\cdots,\pi_n)$。
还有一个(n+1)-th policy记为BR，是我们训练的对每个population member的BR。  
我们需要要优化的是每个policy和BR之间的XP-performance。Loss定义如下：
$$L(BR,\pi_1,\cdots,\pi_n) = -[\sum_{i=1}^n(J_{XP}(BR, \pi_i)+J(\pi_i))+J(BR)+\alpha Diversity(\pi_1,\cdots,\pi_n)]$$
$J_{XP}$是前面定义的 XP-performance，Diversity是某种population-diversity的度量(后文中会以TrajeDi替代)，$\alpha$是可调节的权重。  
由两个core intuitions:
+ 我们可以将整个population看作一个训练集，其目标是guiding the learning of BR。在测试时，BR会compatible with larger set of possible XP partners。
+ *BR classes hypothesis*: 一个好的SP policies space可以被划分为不同的BR class。两个不同的policy可以被划分到同一类 if and only if 他们有相同的BR。$J_{XP}(\pi_1,\pi_3) = J(\pi_1) \ and \  J_{XP}(\pi_2,\pi_3) = J(\pi_2)$。另外我们假设更大的BR class有更小概率出现。在Hababi等设定中，众多的policy都有各自的BR，即class size为1.
需要指出的是，很多环境中最大化SP分数和BR class没有关系，因此前述的loss优化可能和SP分数没有关系。

### trajectory diversity
为了计算上述loss，我们想找到一个diversity measure。  
从trajectory角度看，不同的policy之间可以通过不同的trajectory distribution来区分。我们使用了GAN中的JSD。
$$JSD(\pi_1,\cdots,\pi_n) = H(\hat \pi) - \sum_{i=1}^n \frac 1n H(\pi_i)$$
$(\pi_1,\cdots,\pi_n)$ 是population of n policies。H是shannon entropy，$\hat \pi$是trajectory average。上述等式可以表述为average KL divergence from population policies to $\hat \pi$
$$JSD(\pi_1,\cdots,\pi_n) = -\frac 1n \sum_{i=1}^n\sum_{\tau}P(\tau|\pi_i)[\log \frac{\hat \pi{(\tau)}}{\pi_i(\tau)}] = \frac 1n \sum_{i=1}^n D_{KL}(\pi_i||\hat \pi)$$
JSD的好处如下：
+ KL散度是两两配对的，JSD可以可以扩展到任意数量的policy
+ JSD是对称的
+ n=2时，JSD的平方根是一个metric，即最大化前述等式等价于增大在metric space中policy之间的距离。
+ 最大化JSD有一个直观解释：前述等式第一项鼓励population尽量覆盖 $\tau$ 的子集 by maximizing its collective entropy。第二项使得每个policy distribution收缩到尽可能少的trajectory。
这种平衡的结果是JSD实际上不受每个策略的单个熵的影响，这与熵最大化技术完全不同。事实上，它唯一依赖的是policy之间的重叠程度，因此它的界限在0(当所有policy都产生相同的分布)和log n(当所有轨迹分布都不相交)之间。特别地，这意味着JSD的最大值与可能的轨迹数无关。此外，JSD计算中的抽样过程意味着，对于任意给定pair中的policy，它们只需要沿着不同的、拥有非零概率密度的trajectory。换句话说，它抓住了这样一种直觉:在它从来对没有访问过的轨迹树的部分进行评估策略是没有意义的。因此，JSD精确地度量了我们寻求优化的多样性概念

### action discounting
有些复杂环境有很长的horizons或者很大的自由度。  
例如假设在每一条可能的轨迹上，都存在一个null space，其中有两个或两个以上的行为都是最优的，但对agent的感知行为影响甚微或没有影响。这可能是一个无关紧要的动作，比如机器人选择用哪只手臂按按钮，或者多个动作的排列，而顺序与最终结果无关。  
在这种情况下，JSD objective过于敏感，因此算法可能产生大量几乎相同的策略。他们都最大化了目标，但在null space中的操作不同  
理论上，如果policy 足够多以至于使null space饱和，这个问题就可以解决。即有足够多的策略覆盖所有可能的操作排列。但在实践中很难。  
为了解决这个，提出了JSD的推广。给定 $\pi_i \And \tau$，local action kernel定义为
$$\delta_{i,t}(\tau) := \sum_{t'=0}^T[\pi_i(a_{t'}|\tau_{t'})]^{\gamma^{|t-t'|}}$$
$\gamma$是折扣因子。  
令$\hat \delta_{t}(\tau) := \sum_{i=1}^n \frac 1n \delta_{i,t}(\tau)$为 average local action kernel，用$\delta_{t,i}$来替代 $\pi_i$，用$\hat \delta_t$替代$\hat \pi$,对timestep做平均，我们有
$$JSD_\gamma(\pi_1,\cdots,\pi_n) := - \frac 1n \sum_{i=1}^n\sum_{\tau}P(\tau|\pi_i)\sum_{t=0}^T \frac 1T \log \frac{\hat \delta_t(\tau)}{\delta_{i,t}(\tau)}$$
这就是最终的 TrajiDi objective！  
令 $\gamma = 1$，$\delta_{i,t}(\tau) = \pi_i(\tau), \hat \delta_{t}(\tau) = \hat \pi_i(\tau) \ for all t$ 我们是在trajectory distribution上计算JSD。   
同样地，$\gamma = 0$，$\delta_{i,t}(\tau) = \pi_i(a_t|\tau_t), \hat \delta_{t}(\tau) = \widetilde{\pi}(a_t|\tau_t) \ for all t$,最大化这一项会产生在每一个state选择不同action的policies。等价于在individual action层面的KL散度。  
如果两个确定性策略，$JSD_0$里面的项只能取0或1，这时$JSD_0$度量的是两个策略“不同意”（选择不同动作）的数量的期望。  
总之，JSD1是一个非常灵敏的trajectory level的目标，它最适合于在最优解决方案中具有很少的自由度的“紧”设置。它的对应版本JSD0是一种更加严格的action level多样性度量，它可以用于推动每个state的policy有所不同。通过调整$\gamma$， TrajeDi允许我们平滑地在两个极端之间插值。  
当$\gamma_1>\gamma_2$时，$\log n \ge JSD_{\gamma1}(\pi_1,\cdots,\pi_n) \ge JSD_{\gamma2}(\pi_1,\cdots,\pi_n) \ge 0$  
我们使用了fixed horizon。如果$\gamma<1$，TrajeDi with infinite horizon也可以估算。

### objective gradient
梯度的计算代价很大。我们给出一个近似：
我们估计$\log(n\hat \delta_t(\tau))$ term in sum by its tangent,得到$\widetilde{JSD_\gamma}$,$\widetilde{JSD_\gamma} \le JSD_\gamma$。假设每个policy用$\theta_i$参数化，有：
$$\Delta_{\theta_i} \widetilde{JSD_{\gamma}} = -E_{\tau~\pi_i} \{ \frac 1T \sum_{t=0}^T \frac{\hat \pi(\tau)}{\pi_i(\tau)}\delta_{i,t}(\tau) \Delta_{\theta_i}\log \theta_{i,t}(\tau)+(\hat \delta_t(\tau)-\frac 1n \log \delta_{i,t}(\tau))\Delta_{\theta_i} \log \pi_i(\tau) \}$$  
其中$\hat \pi(\tau) \And \hat \delta_t(\tau)$都是期望，可以通过采样得到。
因为最外层的期望是在$\pi_i$产生的轨迹之上抽样，所以policy不会从其他policy产生的轨迹上接收梯度。由于第一项中的重要性采样，这会带来更大的方差。

### TrajeDi for ZSC
我们现在可以使用 TrajeDi来计算diversity了，完整的可微的PBT loss为
$$L(BR,\pi_1,\cdots,\pi_n) = -[\sum_{i=1}^n(J_{XP}(BR, \pi_i)+J(\pi_i))+J(BR)+\alpha JSD_{\gamma}(\pi_1,\cdots,\pi_n)]$$
再使用上述梯度估计，每个policy的梯度可以从它自己的replay buffer中计算
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/TrajeDi1.png?raw=true">    
