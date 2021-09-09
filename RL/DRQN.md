# DRQN(Deep Recurrent Q Network)
## Background
+ POMDP
+ DQN

## Contribution
解决了DQN在POMOP情况下的使用

## Task
很多rl的环境是POMDP的,例如使用画面作为输入的Atari游戏.一种解决方法是输入前后数帧的图像.
## Solution
**DQN**  
+ 作为DQN的变体,DRQN的基本特征和DQN是类似的,例如双网络以及Replay Buffer  
+ 在DQN中state定义为前四帧的画面,通过conv和fc层得到Q value  

**DRQN**  
与DQN相比,将conv后的fc层替换为了LSTM层.有两种更新方式:
1. *序列更新*: 
+ 从replay buffer中选取一个episode,使用从开始到结束的所有step.训练过程中LSTM的hidden state从上一时刻继承.
+ 能够更好的让LSTM学习一次游戏过程的所有时间序列记忆，更有利于时序推理.
+ 序列化采样学习，数据有马尔可夫性
2. *随机更新*: 
+ 从replay buffer中选取一个episode,使用从随机某个step到结束的所有step.每次训练前将hidden state置零.
+ 损害了LSTM的记忆能力