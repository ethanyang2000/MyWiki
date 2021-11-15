# [Collaborating with Humans without Human Data](https://arxiv.org/pdf/2110.08176.pdf)

## Introduction
我们希望agents可以和novel partner合作，即ad-hoc, zero-shot coordination，并且在没有人类数据的情况下得到。  
和人类合作需要快速适应人类的行为水平、弱点和偏好。很多RL方法例如self-play(SP)、population play(PP)常常overfit了他们的训练伙伴，不能很好地适应人类行为。  
有一种方法是收集人类数据，训练一个人类模型，然后用于RL的训练，称为behavioral cloning play(BCP).  
我们认为这个问题的关键是得到diverse的训练伙伴，可以从competitive的领域得到灵感。  
先训练population of self-play agents and their checkpoints，然后用于RL训练。这种方法称为Fictitious Co-Play(FCP)。  
实验在overcooked上进行，测试了SP,PP,BCP,FCP。曾经overcooked的SOTA结果是BC。  
实验针对的是一般的回报，所有agents有同一个目标，收到同样的奖励。
+ self-play：只和自己合作
+ population-play：可以在人类合作的团队竞技中，在pure common payoff game中表现一般。这种情况下PP的agent会趋于同质化，降低多样性，和SP类似
+ FCP：有N个self-play agents，他们只有随机种子不同。在训练中保存checkpoints。将这些agent用于训练最终的产物。  

contributions:
+ 提出FCP用于zero-shot coordination with humans
+ 证明FCP在zero-shot coordination with a variety of held-out agents的任务中优
+ FCP outperforms BCP
+ 人机交互研究方法

## Methods
### FCP
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/FCP1.png?raw=true">
一个重要问题是打破symmetries。agent应该根据队友的变化做出选择，即不对称的偏好。另外对于熟练和不熟练的队友，即不同的skill，都应该能适应。
FCP包括两个阶段：
1. 训练一个diverse pool of partners，包含N个self-play agents。这些agent是单独训练的，可以break symmetries。另外为了表示不同水平，我们对每个agent保存了一些checkpoints
2. 训练一个FCP-agent。FCP需要适应pool中的agents。  
训练过程使用了V-MPO，ResNet+LSTM。每个episode从population中选两个出来进行游戏。  
PP & FCP 使用了N=32，均匀采样。FCP使用了 3 个checkpoints。对于architecture diversity，我们使用了有无LSTM，网络宽度16和256四种组合，每种8个，保持N=32.
BCP的数据集一半用来训练，一半用于evaluation。

### Baselines and ablations
一共有三个baseline：
+ self-play
+ population-play
+ behavioral cloning play
做了如下ablation study:
+ $FCP_{-T}$代表没有checkpoints的训练
+ $FCP_{+A}$代表加入了 architecture diversity
+ $FCP_{-T,+A}$  

## Environments:
一个研究 zero-shot coordination in human-agent interaction 的环境。  
玩家扮演厨师，目标是尽可能多地上菜。两个玩家共享奖励。agent需要学习的是在环境里导航，并和道具互动。另外也要注意同伴的行为。
+ observation： RGB view
+ actions: stand still, move{up,down,left,right}, interact.

## zero-shot coordination with agents
evaluation with hold-out agents
### collaborative evaluation
和人类测试代价过大，我们使用hold-out agents
1. BC model trained on human data
2. a set of self-play agents varying in seed, architecture and training time
3. randomly initialized agents
结果展示了每个episode平均送餐数。
1. FCP outperforms all baselines
2. training with past checkpoints is the most beneficial variation for performance
去掉checkpoints显著降低了表现，增加architectural diversity不会增加表现。
但是去掉checkpoints并加入architectural diversity会优于baseline

### zero-shot coordination with humans
run an online study to evaluate our FCP agent and the baseline agents in collaborative
play with human partners.
每个受试者和所有agents进行互动，每个agent互动 1min。每互动两个agent之后打分。


