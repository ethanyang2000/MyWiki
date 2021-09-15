# Credit Assignment
## Introduction
大多数MARL算法都面临信度分配/回报分配/credit assignment问题。具体是指当多个Agent同时执行任务时，如何合理评价每个Agent的效用。
## Classification
+ **自上而下**： 这种类型通常指我们只能拿到一个团队的最终得分，而无法获得每一个 Agent 的独立得分，因此我们需要把团队回报(Team Reward)合理的分配给每一个独立的 Agent(Individual Reward)，这个过程通常也叫 “独立回报分配”(Individual Reward Assign),典型的代表为COMA算法。  
+ **自下而上**： 指当我们只能获得每个 Agent 的独立回报(Individual)时，如何使得整个团队的团队得分(Team Reward)最大化，例如QMIX算法。
