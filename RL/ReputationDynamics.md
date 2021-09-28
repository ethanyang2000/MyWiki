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
