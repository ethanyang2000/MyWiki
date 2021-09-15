# [MAVEN](https://arxiv.org/pdf/1910.07483.pdf)
## Introduction
1. 分析了CTDE的常见算法QMIX和QTRAN。  
QMIX由于单调性约束，无法进行有效的探索，常收敛到次优。
2. 提出一种改进：MAVEN，Multi-Agent Variational Exploration  
通过引入一个层次化控制的潜在空间，混合了基于价值和基于策略的方法，让各个基于价值的智能体的行为取决于共享的潜在变量，而潜在变量由层次策略来控制。这使得MAVEN能够拥有一种更好的探索方式。

## Analysis
QMIX通过单调性约束对value function进行分解，这种分解使得每个agent可以单独执行动作，但也使QMIX收敛到次优。  
QTRAN将其转换为线性约束的优化问题，并使用了L2正则化减少计算量。  
无效的探索方式不仅对单智能体的强化学习有损害，还使得总体无法学得最好的策略。单智能体可以通过提高探索率 $\epsilon$ 等方式来解决，但这些方式在MARL中是不可行的。而Committed exploration是一个可行的办法。  
对于MAVEN，固定潜变量，每个action-value联合函数可以看作在一个episode中存在的联合行为探索的模式。此外，MAVEN最大化了trajectories和潜变量之间的互信息，以学到更多样的行为。这使得MAVEN在满足约束的同时实现committed exploration

## Background
1. Dec-POMDP  
$$G = $$
2. IGM
3. QMIX
4. QTRAN

## Methods
### Structure
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/MAVEN.jpg?raw=true">
左边是隐空间策略的学习框架。我们将分层策略用$\theta$参数化，将hypernet map用$\phi$参数化。其中hyper net map将潜变量$z$映射到一组权重$W$，用来影响每个agent的效用（或者*modifying the utilities for a particular mode of exploration*）。mixer net用$\psi$参数化。  
中间是每个agent的actor，用来计算agent的效用，用$\eta$参数化。每个agent的action-obs pair经过GRU和MLP后再和$w$混合，生成Q-value。Q-value用$\epsilon -greedy$策略处理后经过mixer net产生联合q-value。  
分层策略用$\pi_z(\cdot|s_0;\theta)$表示。可以看作是一个随机变量$x~p(x)$经过以$\theta$参数化的神经网络形成的变换。$p(x)$可以是均匀分布或正态分布。从神经网络的视角看可以记作$z~g_0(x,s_0)$，$s_0$为初始状态。  
右侧是MI计算网络。输入为轨迹$\tau$，其中包含每个agent的效用和全局state。这里效用在使用前经过$\sigma$处理，其为Boltzmann策略，以保证可微性。输入通过RNN编码，编码后的hidden state作为discriminator的输入。discriminator用$v$参数化，通过变分推断得到互信息的近似，记为MI loss。这里可以看作diverse critic

### optimization
使用coordinate ascent
固定一组$z$，有联合action-value function $Q(u,s;z,\phi,\eta,\psi)$,显然也有一个$\epsilon -greedy$策略 $\pi_A(u|s;z,\phi,\psi,\eta)$。由此得到Q-learning loss：
$$L_{QL}(\phi,\eta,\psi) = E_{\pi_A}[(Q(u_t,s_t;z)-[r(u_t,s_t)+r\max_{u_{t+1}}Q(u_{t+1},s_{t+1};z)])^2]$$
其中t代表某个时间步。  固定$\phi,\psi,\eta$，分层策略$\pi_z(\cdot|s_0;\theta)$ 使用累积奖励$R(\tau,z|\phi,\psi,\eta)$训练。其目标函数用于保证探索的动作z的回报值最高。为：
$$J_{RL}(\theta) = \int R(\tau_A|z)p_\theta(z|s_0)\rho(s_0)dzds_0$$  
到目前为止，我们并没有鼓励多样化的行为（不同的$z$），$z$可能会塌缩到同一个行为分布。为了避免这样，我们引入了轨迹$\tau = {(u_t,s_t)}$和潜变量$z$之间的互信息，即RNN的输出$MI(\sigma(\tau),z)$,并将其最大化。通过最大化z的信息增益来保证探索策略模式的多样性。MI objective写做：
$$J_{MI} = H(\sigma(\tau))-H(\sigma(\tau)|z) = H(z)-H(z|\sigma(\tau))$$
不过，$\sigma(\tau)$和$p(z|\sigma(\tau))$均难以计算，因此上式不能直接使用。这里采用了变分推断的方式进行估计。我们记一个变分分布$q_v(z|\sigma(\tau))$（由$v$参数化）来估计$z$的后验分布，并得到了一个$J_{MI}$的下界：
$$J_{MI}\ge H(z) + E_{\sigma(\tau),z}[\log(q_v(z|\sigma(\tau)))]$$
不等式右侧记为变分MI目标$J_V(v,\phi,\psi,\eta)$，当变分推断完全准确时等号成立。
$$E_{\tau,z}[\log(q_v(z|\sigma(\cdot)))] = E_{\tau}[-KL(p(z|\sigma(\cdot))||q_v(z|\sigma(\cdot)))] - H(z|\sigma(\cdot))$$
由于KL散度的非负性，变分推断的误差会影响模型表现。变分推断也可以看作discriminator/critic,即引入了auxiliary reward field on trajectory space
$$r^z_{axu}(\tau) = \log(q_v(z|\sigma(\tau))) - \log(p(z))$$
总体loss为：
$$\max_{v,\phi,\eta,\psi,\theta}J_{RL}(\theta) + \lambda_{MI}J_V(v,\pi,\eta,\psi) - \lambda_{QL}l_{QL}(\phi,\eta,\psi)$$

## pipeline
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/MAVEN1.png?raw=true">
+ 训练过程中，在每个episode开始时sample一个x，并计算潜变量z，然后按照policy展开。  
采样完成后保持当前的exploration mode(z)和变分MI奖励，利用greedy policy和Q-learning loss训练$\phi,\eta,\psi,v$。接着利用实际的回报对分层策略进行优化($\theta$)  
+ 在测试时，在episode开始时采样一个$z$，然后对每个q-function使用$arg\max$进行去中心化执行。