# [QMIX](https://arxiv.org/pdf/1803.11485.pdf)
## Background
+ QMIX是一种value based marl算法，可以看作Actor-Critic和DQN的结合。
+ 是一种CTDE方法，利用centralized critic接收全局信息，指导Actor更新。
+ Critic利用TD-error更新，并且有evaluate net和target net，和DQN类似
+ 学习到分布式策略，本质上是值函数逼近算法，**只能用于合作环境**
+ 针对Dec-POMDP
+ 考虑如何通过局部值函数构造联合值函数, 借由全局状态信息提升算法
+ 考虑保证联合值函数与局部值函数单调性相同

## Task
credit assignment

# Contribution
+ VDN的改进，考虑只通过全局的奖励信息R指导学习
+ VDN -> QMIX -> Qatten

## Solution
## 模型结构
<img src="https://github.com/EthanYang233/MyWiki/blob/master/pics/QMIX.jpg?raw=true">  
QMIX使用了CTDE的AC框架，整个网络包含了Mixing Network(critic)和Agent RNN Network(actor)。
+ Agent RNN Network
QMIX中每一个 Agent 都由 RNN 网络控制，在训练时你可以为每一个 Agent 个体都训练一个独立的 RNN 网络，同样也可以所有 Agent 复用同一个 RNN 网络。
+ Mixing Network
Mixing Network相当于 Critic ，同时接收 Agent RNN Network 的 Q 值和当前全局状态 $s_t$ ，输出在当前状态下所有 Agent 联合行为的效用值 $Q_{tot}$ 。  
Mixing 同样使用神经网络结构，不同的是，上图中蓝色部分（中间层神经元）的权重（weights）和偏差（bias）均由右边红色的神经网络产生。即，Mixing 网络中实际包含两个神经网络，红色参数生成网络 & 蓝色推理网络。
    + **参数生成网络**： 接收全局状态 $s_t$ ，生成蓝色网络中的神经元权重（weights）和偏差（bias）。
    + **推理网络**： 接收所有 Agent 的行为效用值 Q ，并将参数生成网络生成的权重和偏差赋值到网络自身，从而推理出全局效用 $Q_{tot}$ 。

## 训练过程
QMIX 的更新方式和 DQN 非常类似，设定 evaluate Net 和 target Net，并利用 TD-Error 完成参数更新。
$$loss = TD-Error = Q_{tot}(evaluate) - (r + \gamma Q_{tot}(target))$$
一共存在两个 Mixing 网络（evaluate & target），两个网络分别用于产生 $Q_{tot}(evaluate)$ 和 $Q_{tot}(target)$，两个网络接收不同的输入.
+ **eval net**: 接收在状态 s 下每个 Agent RNN Network 所选行为的 Q 值作为输入，输出 Qtot(evaluate) 。
+ **target net**: 接收在状态 snext 下每个 Agent RNN Network 所有行为中最大的 Q 值作为输入，输出 Qtot(target)。

## 理论
+ 如何对局部Q值非线性组合，并加入全局状态信息？
保证联合Q与局部Q单调性相同，这样对联合Q取$argmax$等于对每个局部Q取$argmax$。同时联合Q对局部Q的偏导数大于0.
+ Q网络的设计
混合网络权值非负，状态通过abs函数后再输入