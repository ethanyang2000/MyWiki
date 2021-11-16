# [A Unified Game-Theoretic Approach to Multiagent Reinforcement Learning](https://arxiv.org/pdf/1711.00832.pdf)

## abstract
使用independent RL解决MARL有很多问题，训练的agent会overfit其他agent。  
常用的解决办法包括：
+ 引入额外信息，如joint values
+ use adaptive learning rates
+ adjust the frequencies of updates
+ dynamically respond to other agents actions online  
然而上述方法大部分都集中于矩阵博弈或者fully observable的情况，这太naive了.也有很多工作考虑了partially observable的情况：如果model可知，而且two-player是完全敌对的，则可以使用policy iteration method based on regret minimization。再加上domain-specific abstraction，他们可以拓展到很多agent的情况。  
然而这些工作需要比较复杂的model，研究者会使用近似形式的model来保证好的性能。  

1. 引入新的指标来表示correlation effects of policies，他们都是由独立算法在MARL环境中学出来的
2. 展示了overfit问题的严重性
3. 当环境变的更加partially observable的时候，overfitting问题变得更严重
4. 我们提出基于economic reasoning的新算法，称为Policy-space Response Oracles (PSRO)，具体这么做的：
    + 给定一组策略及他们出现的分布，RL算法学会一个best response
    + 使用博弈论分析来计算新的meta-game的策略

## background
### normal form game
($\prod, U, n$) where n means number of players, $\prod = (\prod_1, \prod_2, \cdots, \prod_n) $ means a set of policies. $U:\prod -> R^n$ is a payoff table of utilities for each joint policy played by all players. Extensive normal games 将这个设定扩展到多步的序列问题。  
players 目标是最大化收益，具体做法是从 $\prod_i$ 中选择一个policy $\sigma_i \in \Delta(\prod_i)$, where $\Delta$ means a distribution over $\prod_i$。在多智能体环境中，$\sigma_i$的度量取决于对手的策略。每个finite extensive form game都可以等价为normal form。  
有一些已有的算法，例如在零和游戏中：
+ linear programming
+ fictitious play
+ replicator dynamics
+ regret minimization

### double oracle
DO算法解决了一类两个玩家、normal form的游戏，即一个subgame，$\prod^t \subset \prod$ at time t.  
a payoff matrix for the subgame $G_t$ includes only those entries corresponding to the strategies in $\prod_t$,即策略空间子集。 这个策略空间中的每个策略对打，可以得到一个payoff matrix  
每一时刻t，都可以得到纳什均衡 $\sigma^{*,t}$ obtained for $G_t$  
当我们得到了纳什均衡之后，对于两个player，我们都可以找到他们的best response(在其他人选择的策略不变的情况下，能够最大化$player_i$的payoff的策略，称之为best response)。
to obtain $G_{t+1}$ each player adds a best response $\pi_i^{t+1} \in BR(\sigma_{-i}^{*,t})$ from the full space $\prod_i$, so for all i,$\prod_i^{t+1} = \prod_i^t \cup \{\pi_i^{t+1}\}$  
这里有点混淆two-player的一些性质。如果两个player是对称的，自然可以使用同一个策略空间。如果不对称的话，那就分成两个策略空间，分别维护就好。显然，double oracle algorithm可以保证收敛于two-player game的Nash equilibrium ,但在最差的情况下，整个策略空间都需要被遍历。
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/PSRO.png?raw=true">

### Empirical game-theoretic analysis(EGTA)
建模一个meta-game，这样策略空间就小得多了，我们不需要考虑所有agent的所有action，只需要考虑agent与agent交互之后带来的结果，比如胜率、生存率、得分等等。

## Policy-Space Response Oracles
Policy-space Response Oracles (PSRO)方法是double oracle algorithm的自然延伸。只不过DO的meta-game操作的对象是action，而我们操作的对象是策略(meta-game的所谓action指的是挑选一个action)。  
每一个player，都存在一个"strategy space"，在实际比赛中，每一个player的位置上，我们都会从这个space中以一定的概率分布取出一个策略，放到环境中这个player的那个位置上。如果是对称的两个玩家的博弈，我们可以只维护一个策略的池子，但如果场景不对称的话，就只能每一个player维护一组策略了。  
define $\prod^T = \prod^{T-1} \cup \{\pi'\}$ as the policy space 
算法流程如下：
+ 我们手头有一个已知的策略种群。如果是two-player、非对称的博弈的话(即左右两边不能对调)，那我们手头就有两个种群，一个是player1的，一个是player2的。当然，如果是MARL的话，你可能有多个player，自然也有多个对应的种群。
+ 我们已知了现在的纳什均衡，即player1有一个挑选策略的分布，player2有一个挑选策略的分布。
+ 现在我们想训练player1的话，我们就固定住player2挑选策略的概率分布。
+ 然后直接从头开始训练一个新的策略，让他和player2给的策略对打(best response)。
+ 每个epoch为每个player加入一个新的policy
+ 每个epoch，每个player中有很多episode，所有episode训练的是同一个策略，但是每个episode开始的时候其他agent的策略是重新采样的。
+ 每个epoch重新计算效用和meta-strategy
+ 最开始的时候strategy space内只有一个agent
+ 当我们把对手的分布固定住，只训练一个agent的时候，这是符合马尔科夫性的，best response也是可以用任何算法训得出来的。
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/PSRO1.png?raw=true">
naive self-play，又称fictitious play，是Policy-space Response Oracles (PSRO)的一个特例，选择对手的概率分布为：最新加入league的对手概率为1，其他为零。问题是它无法对广泛的对手做出反应，只能过拟合到最新的对手上。  
double oracle algorithm也是一个特例，根据Nash equilibrium给出的概率来挑选对手。他的问题是
无法泛化到之前没有见过的那些策略空间上。可能会过拟合到某一个特定的纳什均衡上。

## Meta-strategy
Meta-strategy
以league和payoff作为输入(即以empirical game$(\prod, U^\prod)$作为输入，以选好的对于每一个player的挑选策略的概率分布作为输出($\sigma_i, i = 1,\cdots,n$)  
在给定所有player的策略分布后，可以得到一个player的预期收益：$u_i(\sigma), \sigma = [\sigma_i,\cdots,\sigma_n]$  
也可以得到所谓的counterfactual的value，即当我们把第i个player的策略，固定为这个player的strategy-space中的第k个策略，而其他player的策略分布不变的时候的收益：$u_i(\pi_{i,k}, \sigma_{-i})$
本文引入一个exploration parameter $\gamma$。其实就是把DQN里面的epsilon-greedy exploration拓展到了有许多个player，每个player有许多个候选策略的情况。对于每一个player而言，他手下的每一个策略都有最小这个概率选中它：$\frac \gamma{K+1}$，K当然是这个player的league的大小。  
使用了三种meta-game solver：regret-matching, Hedge, projected replicator dynamics来解这个meta-game。前两种是前述算法的直接应用。

## PRD
### replicator dynamics
replicator是evolutionary algorithms中的术语，指的是可以自我复制的东西。在我们的语境下当然指的就是policy。  
Replicator Dynamics给出一组微分方程，用来描述种群的进化，最基本的形式如下：
biological selection principle
$$\frac {dx_k}{dt} = x_k[(Ax)_k-x^TAx]$$
式子中x是一个长度为K的向量，每个元素表示在这个总共有K个个体的种群中，挑选其中之一出来的概率。也就是说x就是上文出现的$\sigma_i$，i指某一个league。  
式子中A表示payoff矩阵。即场上只有两个个体的话，个体之间“对打”的结果，比如胜率、赚了多少分或者生存概率等等。  
显然，左边这一项$(Ax)_k$表示的就是个体k，当依概率x取一个对手的话，平均而言存活(获胜)的概率。  
右边这一项则表示这个种群平均下来，依照概率x取俩个体“对打”的话，平均而言的概率。  
所以方括号这一项有一点PPO里面advantage的味道。方括号外面再乘一下某个体被选中的概率xk就完事了。  
这个形式适合于symmetric game，通常作为囚徒困境、matching pennies、stag hunt games等问题的dynamics。

### Projected Replicator Dynamics
在非对称博弈中，Replicator Dynamics的规则可以修改成：
$$\frac {dx_k}{dt} = x_k[(Ay)_k-x^TAy]$$
$$\frac {dy_k}{dt} = y_k[(x^TB)_k-x^TBy]$$
现在我们的payoff矩阵不再是$U_{\prod} \in R^{n \times n}$，而是$U^{\prod} = (A,B), A\in R^{n_x \times n_y}, B \in R^{n_x \times n_y}$
这很合理，只是简单的修改。问题在于什么叫“projected”？
本文引入projection operator $P(\dot)$ 来保证探索。
$$x <- P(x+\delta \frac{dx}{dt})$$
$$y <- P(y+\delta \frac{dy}{dt})$$
$$P(x) = x \ if \ x_k \ge \frac{\gamma}{K+1}$$
$$P(x) = \argmin_{x' \in \Delta_{\gamma}^{K+1}}||x'-x|| \ if \ x_k < \frac{\gamma}{K+1}$$
$$\Delta_{\gamma}^{K+1} = \{x_k \ge \frac{\gamma}{K+1}, \sum_k x_k=1 \}$$
注意到其实这个projection的目的就是让一个league中的每个policy都有一个最小的概率被选中。这个最小概率就是 $\frac {\gamma}{K+1}$

### Deep Cognitive Hierarchies
上面说的迭代训练的方法听起来很好。但是求得best response的RL步骤特别慢。而且下一次迭代的时候，如果从头训练的话，又要重新学习一些基本的behavior。作者提出，其实我们可以把整个的训练过程并行起来，只需要保证每一个策略学习的时候他的“对手们”是正确的即可。即使这些“对手们”并没有彻底训练完成。  
相较于无限epoch的训练，我们先指定一个固定的 levels k，现在我们有一个n-player博弈。我们会打开nK个进程。  
每一个进程会训练一个独立的策略 $\pi_{i,k}$ ，这里我们称之为level k的策略，并更新meta-strategy $\sigma_{i,k}$  
每个进程保存$\pi_{j,k' \le k}$，即  at the current and lower levels , as
well as the meta-strategies at the current level$\sigma_{-i,k}$，这个拷贝会定期更新。
因为每个进程使用的是过去的拷贝，DCH是PSRO的一个近似。  
注释2：现在，假设我们要训练player i中的level k的agent。我们需要求一个关于所有level k-1的情况下可能出现的对手的best response。注意，此时每一个player都有k-1个agent，总共有n(k-1)个policies，需要维护一个$(k-1)^n$ 的payoff matrix。因此需要输入的信息是：$n \times (k-1)$个policies，以及n个长度为k-1的概率向量。  
每个进程储存nK个固定的policies，n个meta-strategies(size of them bounded by $k \le K$)。总空间复杂度为$O(nK\dot (nK+nK)) = O(n^2K^2)$

### Decoupled Meta-strategy Solvers

