# [DIAYN(diversity is all you need)](https://arxiv.org/pdf/1802.06070.pdf)
## introduction
生物体可以在无监督的条件下学习技能，探索环境，对新任务适应性很高。在无监督的条件下学习有很多优点
+ 解决稀疏奖励
+ 更好地探索环境，发现多样化策略
+ 可以作为模型的初始化参数
+ 不用设计奖励函数
我们希望在完全没有奖励的情况下训练，并假设为了完成目标，我们需要训练出尽可能多的行为策略。其核心的方法就是将skills之间的discriminability作为优化目标。并且两种可区分的skills并不一定有很大的差别。我们希望通过得到尽可能多样的行为，使得策略对扰动具有鲁棒性，并尽可能地对环境进行探索。
综上，我们提出了无监督RL的方法，并证明简单的探索可以得到多样的行为。这种方法可以用于分层RL的底层策略，可以很快适应新环境，也可以用于模仿学习。

## DIAYN
具体来看，我们的无监督RL范式分为两部分：一部分无监督的探索和一部分监督学习。无监督阶段的目标是均匀地学到更多技能，使得监督阶段最大化task reward变得更容易。由于无监督阶段没有用到task的先验，因此可以用在不同的task中。
### basic ideas
+ 对于有用的skills，我们希望每个skill对应到不同的states
+ 希望通过states而不是actions来区分skills
+ 通过随机skills使得agent尽可能地探索state space
最重要的，此方法最大化skills和states的互信息($I(A;Z)$)，实现idea that the skill should control
which states the agent visits.另外，这个互信息也说明我们可以从state推测出skill。  
为了确保state而不是actions用来区分skills，我们最小化skill和actions的互信息($I(A;Z|S)$)。另外将所有skills看作mixture of policies，然后最大化熵$H[A|S]$。  
综上，我们最大化的目标是：
$$
F(\theta) = I(S;Z) + H[A|S] -I(A;Z|S)\\
= (H[Z] - H[Z|S]) + H[A|S] - (H[A|S] - H[A|S,Z])\\
= H[Z] - H[Z|S] + H[A|S,Z]
$$
上述公式第一项鼓励先验分布$p(z)$有较高的熵。第二项suggests that it should be easy to infer the skill z from the current state,$S$和$Z$之间尽量一一对应，不确定性尽量减小。第三项希望在给定状态和Skill的情况下,每个skill能尽可能随机。  
但我们不能直接计算$p(z|s)$，因此我们使用一个discriminator $q_\psi(z|s)$ 来近似这个后验分布。这个变分推断给出了一个$F(\theta)$的ELIBO$G(\theta,\phi)$
$$F(\theta) \ge H[A|S,Z] + E_{z~p(z),s~\pi(z)}[\log q_\phi(z|s) - \log p(z)] = G(\theta,\phi)$$

### structure
主要包括两个网络：一个是策略网络（下图中的蓝色框 SKIIL），它和通常策略网络的区别在于，它还接受一个控制该策略的参数z，用于生成不同的策略；另外一个是一个判别器网络（下图蓝色框中的 DISCRIMINATOR），它的作用是给定一个由参数z控制的策略方位的状态，输出该状态可能对应参数z的概率分布。即当Agent已经访问了状态$s_{t+1}$，则这个状态是一个特定的参数$z$所访问到的概率分布.
给定一个状态，学习一个判别器来预测这个状态最可能被哪个参数z控制下的策略访问到。同时，给定一个参数z，希望它对应的策略能够访问到和其他参数不一样的状态空间，因此训练该策略的奖励函数可以由前面学习到的判别器来给出，即如果访问到了一个关于参数z”特殊“的状态，就给予较大的奖励。

### Pipeline 
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/DIAYN.jpg?raw=true">
算法流程：
第一步，采样一个$z$。初始化环境。
第二步，与环境交互，得到$s_{t+1}$，每步的$a_t$的产生都受到$z$的影响。
第三步，利用$s_{t+1}$和$z$计算伪奖励(Pseudo-reward)
第四步，使用SAC算法最大化累计奖励，更新policy
第五步，使用SGD更新判别器参数。

### Implementation
基于soft-actor-critic实现了DIAYN，学习一个基于潜变量的策略$\pi_\theta(a|s,z)$。
可以将task reward替换为pseudo-reward以优化上述ELIBO：
$$r_z(s,a) = \log q_\phi(z|s) - \log p(z)$$
先验分布$p(z)$使用了categorical distribution。  
在无监督阶段，我们在每个episode开始之前采样一个skill$z~p(z)$，然后在episode中按照$z$执行动作。agent rewarded for visiting states that are easy to discriminate。discriminator is updated to better infer the skill z from states visited. 还使用了SAC中的entropy regularization

## application
### 用于策略初始化
这一点比较直观，就直接把学习到的策略网络权重作为权重初始化。唯一不一样的是，DIAYN 学到的策略网络还接受一个z的输入，可能用于权值初始化的时候把与之相关的权重去掉。

### 分层强化学习
把学习到的 skills 用于作为 option（action），另外训练一个 meta-controller 来在不同的 skills 上做选择，meta-controller 的学习使用普通的强化学习算法就好。这里设计了两种比较困难的任务（具体见原文），可以看到把 DIAYN 学习到的 skills 作为 option 效果比直接学习更好。这里还提到了一种使用一些先验知识使得学习到的 skills更为有用的办法（即DIAYN+prior）。比如，对于蜘蛛来说，我们希望它探索出来的不同策略主要反映在它能够到达的位置的不同，就可以在判别器前加上一个先验，即$q_\phi(z|s) \to q_\phi(z|f(s))$，而$f(s)$只反映蜘蛛的质心位置。

### 模仿学习
给定专家的轨迹之后，挑选出来一个$z^*$使得它能够最大可能地产生专家轨迹上的状态，然后把对应的策略作为模仿学习的策略。
$$\hat z = \argmax_z \prod_{s_t\in \tau^*} q_\phi(z|s_t)$$

### meta-learning
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/DIAYN4.jpg?raw=true">
主要利用了 DIAYN 能够自动产生奖励函数（学习目标）的特点，来避免 meta-learning 中需要人为设计任务的环节。这里 meta-learning 的设定如下。给定一个没有 reward 的 MDP ，即 controlled Markov process （CMP），$C = (S,A,T,\gamma,\rho)$，其中最后一个代表初始状态分布。在该环境下进行探索和学习，之后在给定特定奖励函数之后，希望能够快速地学习到一个较好的策略。大致流程如下图所示。

算法过程如下，先使用 DIAYN 得到一个判别器（其实就是得到一族有意义的奖励函数，或者一族自动生成的 MDP），然后在这一族任务中使用 model-agnostic meta-learning （MAML） 来学习。
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/DIAYN1.jpg?raw=true">

### 关于MAML
补充一下 MAML 的学习的主要思路。其目标是给定一组任务，希望能够找到一个参数控制的”中间“策略，使得给定一个具体任务的时候，该策略能够快速地调整到该任务下较好的策略。其训练目标如下：
$$\max_\theta \sum_{\tau_i~p(\tau)}E\left [\sum_t R_i(s_t) \right ] \theta ' = \theta + \alpha E_{\pi_\theta} \left [ \sum_t R_i(s_t) \Delta_\theta \log \pi_\theta(a_t|s_t) \right ]$$
其中， $\theta$是中间策略的策略参数。训练的时候，每给定一个任务$\tau_i$之后，就使用“中间”策略做一次 rollout，然后使用相应的数据做一次策略梯度更新（右边的式子），得到相应的策略参数$\theta_i'$ 。目标就是希望调整$\theta$希望做过一次策略梯度更新之后的策略 $\theta_i'$ 在任务 $\tau_i$ 下表现最好。