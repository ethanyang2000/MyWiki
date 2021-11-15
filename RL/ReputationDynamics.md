# [Reputation Dynamics(Cooperation and Reputation Dynamics with Reinforcement Learning)](https://arxiv.org/pdf/2102.07523.pdf)

## abstract  
对合作行为设计合适的奖励非常困难。一种可能的方法是引入 reputation，使agents为合作行为付出短期的代价，换取一个好的reputation，进而得到未来的好处。博弈论模型说明了特定的社会规范会得到稳定的合作，但是agents如何自己独立学习建立有效的reputation机制仍不清楚。  
但是在RL模型中引入reputation会产生两个问题：
+ agents需要在现有的reputation下学习合作
+ 形成一种共识的社会规范并进行基于行为的reputation评估  


这两个条件会产生一系列均衡点，其中一些会产生有效的合作。  
这里我们使用了引入 reputation 的 Q-learning，结果常常收敛到undesirable equilibria。有两种机制可以缓解：
+ 在系统中加入一定数量的fixed agents，引导agents的训练。
+ 基于introspection的内在奖励。augmenting agents’ rewards by an amount proportionate to the performance of their own strategy against themselves.
以上的组合可以稳定合作的策略，即使在fully decentralized环境中，agents同时学习和使用reputation。

## Introduction  
有两种常见的互惠机制：
+ *直接互惠*  
它允许代理重复会面，从而产生惩罚过去背叛的动机，通过tit-for-tat等互惠策略使合作可行。
+ *间接互惠*  
当代理是匿名的或者不能重复交互时，可以使用信誉机制来约束其合作行为，例如只与信誉好的代理合作。这就是所谓的间接互惠，它允许代理以合作的直接成本换取保持其声誉的未来利益。间接互惠模型可以作为理解基于声誉的系统的工具。间接互惠的框架通常依赖于进化博弈论(EGT)的工具集。在EGT中，agent并不求解均衡，而是复制在进化启发下的动态过程中成功的其他agent。在群体中表现良好的个体更有可能将其策略或政策传递给后代。  

在EGT中，策略集是预先给定的，agent随机进行探索。这里我们使用了RL模型，使agent从简单的行为开始探索状态空间。

## Preliminaries  
### Prisoner's Dilemma
### Reputation Mechanisms  
每个回合N个agents两两随机配对进行游戏，每个episode定长。  
agent的行为用0/1来描述，包括cooperation和compete。 reputation也用0/1来描述。  
每个agent的行为由四位二进制来表示，每一位代表对手/自己reputation的一种组合(01,10,00,11)。即策略描述了所有reputation可能组合下的行为。策略共有 $2^4=16$ 种。
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/reputation1.png?raw=true"> 

### Social Norms and reputation assignment
Social norm 被用来决定agents如何分配reputation，即一个将actions和interactions映射到reputation的函数。一个observer，通常是centralized，根据social norm不断地更新reputation。  
同样social norm也可以用四位二进制数来表示，规定了当互动的两个agent是某种组合时(00,01,10,11)分配给对手的reputation(0,1)。 
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/reputation2.png?raw=true">
social norm 也可以包含更多的信息，扩展到高阶的norm。如果reputation分配有一些error不会影响动态的特性。  
考虑reputation assignment的两种不同形式，采取了不同程度的centralization：
+ ```semi-centralized```(top-down):   
所有agent使用一个固定的外部norm。系统的state由N个action rules提供，每个rules $p_i \in {0...15}$ 
+ ```fully-decentralized```(bottom-up):  
每个agent使用不同的norm，系统state由N个tuple提供 $(p_i, d_i)$。

### EGT stability
在EGT中，如果一个norm和action rule的单态种群可以 resist invasions by any mutant action rule，则这个norm是稳定合作的的。  
一个有效的norm包括两部分：和好的agent合作会有好的reputation；和坏的agent对抗会有好的reputation。其中一种norm称为 Stern Judging。  
但即使有一个好的norm，defection仍然是一种平衡。因此我们可以认为reputation把一个合作问题转换成了简单一些的 stag-hunt 类似问题。但是EGT方法预设了一系列策略，因此我们希望使用RL方法放松这个假设。

## Using Reputation
### Q-learning  
两种不同的机制：
+ agents学习利用reputation，有一个central party提供有效的norm
+ 从个体经验学习reputation
在第一种机制下，我们使用一个固定的有效的norm，但RL仍然不能收敛到合作的均衡，智能体不能利用reputation。这表示了social learning和individual learning之间的gap

## STEERING AGENTS TOWARDS EFFICIENT EQUILIBRIA
### Seeding Agents to Improve Coordination
使用定义好的固定agent

### Introspective rewards
agent关心的是，如果遇到像自己一样的人会发生什么。即一种内省的奖励形式。reward可以写成线性加和：
$$R_i = \alpha U_i + (1-\alpha) S_i$$
其中 $U_i$ 是在特定遭遇中的payoff，$S_i$ 代表他们面对自己时的payoff。这种自我遭遇不会对reputation产生影响，只是用来生成内在奖励。  
除了直觉上的自省，这种机制在EGT中也能得到解释，即 $\alpha$ 控制了选型匹配 assortative matching。在这种机制下agent有更多可能遇到cooperative opponents。

以上的研究是基于centralized reputation的，虽然内在奖励解决了合作，但是agents之间reputation的协调仍然没有解决，即我们假设有一个中央执行者进行reputation assignment，并且每个agent都服从这种规则。

## Learning to assign reputations
对于一个decentralized system，我们在每次遭遇后从种群中随机选取一个第三方，对reputation进行分配。  
agent不仅要学习利用reputation，还要学习分配reputation。这里没有固定的norm，需要agents自己学习。但是reputation assignment并不能带来奖励。