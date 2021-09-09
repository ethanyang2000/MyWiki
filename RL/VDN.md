# VDN(value decomposition network)
## Background
CTDE的一个方法,解决多智能体合作问题中的credit assignment,和COMA类似.不同的是COMA是policy-based,而VDN是value-based
## Task
多智能体合作任务的奖励分配问题,一般有共享奖励的分解和各自奖励的分配.奖励分配中spurious reward signals和lazy agent问题会导致independent RL效果较差.

## Contribution
在多智能体合作问题中将共享奖励产生的值函数分解为几个子值函数,分别指导各个agent的更新.可以看作QMIX的前体

## Solution
将centralized q 分解为每个agent q的加和
1. Q decomposition  
centralized q 的输入为所有agent的action和observation,得到一个共享的Q.而这里假设共享Q值可以分解为每个agent q network 的加和,每个子网络的输入为单个agent的动作和obs,输出为单个agent的Q.  
这里每个子网络并不是严格的动作值函数(没有贝尔曼方程)  
2. 分解的合理性  
需要满足两个条件:  
+ 共享reward可以写成单个reward的和
+ 子网络输入为全局信息  
以上条件二并不能满足,因此将每个agent的历史序列作为输入,部分缓解问题  
集中式训练时只需要计算整体Q函数的TD-error,就可以反传到各个子网络,实现端到端的训练.
3. 参数共享