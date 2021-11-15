# Learning Reciprocity in Complex Sequential Social Dilemmas[https://arxiv.org/pdf/1903.08082.pdf]

## Introduction
合作在社会行为中非常重要，通常合作的行为需要额外奖励的激励。但小规模的社会在这方面表现出了一定的自组织能力，即有一种社会规范，来奖励亲社会者并惩罚反社会行为。tit-for-tat是一种有效的算法，但是只能适用于简单的游戏。一个成功的算法应该有以下几个特征：
+ Nice：start by cooperating
+ clear： easy to understand
+ provocable retaliate against anti-social behavior
+ forgiving: cooperate when faced with pro-social play

互惠模仿的是一系列行为，而不是两种预先确定的行为(纯粹的合作和纯粹的背叛)，这两种行为通常没有明确的定义。其次，互惠不仅适用于两人参与的情况。第三，互惠并不一定依赖于直接观察他人的回报。最后，互惠可以在线学习，提供比计划更好的可扩展性和灵活性.  
我们提出了一种online-learning的互惠模型，解决了上述问题。我们主要把agent分为了两类：innovators和imitators。创新者和模仿者都使用a3c和VTrace进行训练。  
创新者优化一个完全自私的reward。模仿者有两个组成部分:
+ 衡量不同行为的社会性水平的机制;
+ 匹配他人社会性的内在动机。
我们研究了两种评估社会性的机制：第一种是基于环境的手工编码的特征。另一种则训练一个niceness network，该网络估计一个个体的行为对另一个个体未来收益的影响，从而提供了对社会性影响的一个估计。这个network还编码了模仿者之间的社会规范，因为它代表了“一个社会群体成员共享的行为标准”。  
创新者的训练受到模仿者表现出的互惠的影响。创新者如果不表现出亲社会性，这种行为就会很快被模仿者模仿，从而产生负面的收益。  

## agent models
+ *innovators*: 完全使用环境reward进行学习。
+ *imitators* ：学习去match innovators的社会性水平。imitator的设计遵照了上述四点要求。