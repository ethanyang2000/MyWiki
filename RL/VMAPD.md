# VMAPD(Variational Multi-Agent Policy Diversification)
## background
很多MARL算法得到的都是单一的最优解，但很多问题有不止一个最优解，我们希望得到多样化的行为。  
本文提出了一种CTDE的MARL方法，VMAPD。我们将MARL控制问题用概率图模型来表示，并加入了共享的潜变量来揭示trajectory的多样性。然后通过变分推断，我们可以得到VMAPD算法。  
VMAPD算法在共享trajectory上加入了ELIBO，并通过策略迭代最大化ELIBO。实现时我们可以在CT阶段加入一个pseudo reward。

## contributions
+ 将diverse MARL问题转换为联合概率图模型并将一个ELIBO作为优化目标。 The information bottleneck term in the ELBO can prevent the diversity from degenerating to the behavior of a single agent.
+ 使用拉格朗日乘子法引入了改良的ELIBO，以将reward maximization和policy diversification解绑。并且使用了PI控制的原则动态调整拉格朗日乘子。  
+ 提出了VMAPD，只需要调整潜变量就能得到多样的行为。

## Preliminaries
### cooperative MARL with dec-POMDP
### RL with PGM
最优控制问题可以当作概率推断问题来解决。可以用概率图模型描述MDP：
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VMAPD2.png?raw=true">    
我们引入二值随机变量$O_t$，$O_t = 1$表示$t$时刻的动作最优。有概率分布：
$$p(O_t = 1|s_t,u_t) = exp(r(s_t,u_t))$$
可以得到ELIBO:
$$\log p(O_1:T) \ge E_{(z,a_{1:T})~\pi(z,u_{`1:T})}\left [\sum_{t=1}^T r(s_t,u_t)-\log \pi(u_t|s_t)\right]$$
$\pi (u|s)$代表policy。普通的RL算法只优化累计奖励，但ELIBO说明了我们也需要优化一个额外的policy entropy at each visited state。这就是Soft-Actor-Critic方法。

## Methods
### architecture
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VMAPD3.png?raw=true">    

### Diverse dec-POMDP
在PGM中加入潜变量$z$。can discriminate or encode the diverse solutions.为了进行规划并推断潜变量，我们需要求出diverse dec-POMDP的ELIBO，这里我们使用了structured variational inference。在这个方法中，不同部分可以分开优化，即我们可以固定其中一部分。在我们的PGM中，会使用三种approximate functions：  
+ actor networks$q_{\phi_i}(u_t^i|o_{1:t}^i,z)$  
+ global state discriminator$q_\theta(z|s_{1:t+1},u_{1:t})$  
+ local observation discriminators $q_{\theta_{loc}^i}(z|o^i_{1:t+1},u_{1:t}^i)$
固定后两者，即为普通的RL问题。新的ELIBO如下：
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VMAPD1.png?raw=true">    
1. trajectories$\tau$从dataset $D$中采样得到，$B$是baseline  
2. 这里$p(z)$是categorical distribution  
3. *diversity term*： 需要最大化以得到多样的联合行为  
4. *information bottleneck* enhances the diversity of the joint behavior and prevents the diversity from degenerating to the behavior of a single agent.  
5. 很多MARL算法加入了entropy，这里合并在baseline中  
6. ELIBO的各个部分不能简单组合。我们借鉴PI controller使用了adjustable coefficients以稳定训练过程。  

### Modified ELBO with Dynamic Lagrange Multiplier  
diversity dec-POMDP需要在最大化diversity的同时优化累计奖励。这可以表述为约束优化问题：
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VMAPD.png?raw=true">    
使用拉格朗日乘子法将其转化为优化问题。
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VMAPD4.png?raw=true">    
训练过程中需要调整系数$\alpha_t$，使得reward maximization和policy diversification decoupling。开始时要足够小并逐渐增大。  
我们使用差值$e_t = R^t_{exp} - R_{target}$作为反馈。  
$$\alpha_t = \Delta \alpha_t + \alpha_{t-1}$$  
$$where \Delta \alpha_t = K_P[\sigma(-e(t)) - \sigma(-e(t-1))]$$
其中$K$是正的控制超参数

### VMAPD
以MAPPO算法为基础，在policy network和critic中加入了潜变量。$q_{\phi_i}(u_t^i|o_{1:t}^i,z)$和$V_{\psi}(s_{1:t},z)$  
另外还需要加入global discriminator
$$f_\theta(s_{1:t+1}^i,u_{1:t}) = q_\theta(z|s_{1:t+1},u_{1:t})$$
这可以输入global state和joint actions，输出潜变量的分布。另外还有n个local discriminator。  
所有discriminator都是有监督训练的，优化目标如下：
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VMAPD5.png?raw=true">    
为了优化前文的目标，我们构造了一个pseudo reward：
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/VMAPD6.png?raw=true">    
修改后的total reward 可以写成：
$$r_{total}(s_t,u_t) = \alpha_t r(s_t,u_t) + r_z(s_t,u_t)$$
这个reward会存在replay buffer中并用普通RL的方法训练actor和critic  
另外我们重写$\alpha_t$的优化目标：
$$J(\alpha_t) = E[\exp(\alpha_t)e(t)] = E[\exp(\alpha_t)(R_{exp}^t-R{target})]$$
$$\alpha_{t-1}-\beta\Delta_{\alpha_{t-1}}J(\alpha_{t-1}) \to\alpha_t$$