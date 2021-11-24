# [Unifying Behavioral and Response Diversity for Open-ended Learning in Zero-sum Games](https://arxiv.org/pdf/2106.04958.pdf)

## introduction
policy diversity是面对 non-transitive dynamics(存在strategic cycle，例如石头剪刀布)的一个重要问题。其中维护一个diverse policies pool via open-ended learning是一个可能的解决方案。diversity很重要：xxxxx  
但是现在没有很好的diversity的定义，因此也很难研究。这里总结了过去diversity的概念，提供了a unified measure of diversity in multi-agent open-ended learning，基于behavioral diversity和response diversity。  
+ At the trajectory distribution level, we re-define BD in the state-action space as the discrepancies of occupancy measures.  我们假设这个diversity需要用state-action distribution中的不同来区分，因此我们使用了 f-divergence。
+ For the reward dynamics, we propose RD to characterize diversity through the responses of policies when encountering different opponents. gamescape用来表示response capacity of a population of strategies，因此我们使用distance to gamescape，从新的几何视角来处理response diversity
现在的方法没有兼顾两者的。  
在这种度量下，我们设计了一种diversity-promoting objective and population effectivity in open-ended learning。
open-ended learning framework可以用来在零和博弈中产生population of distinct policies via auto-curricula。一种度量diversity的方式是 build metrics over the trajectory or state-action distribution。这种方式只关注behavior，忽视了reward。有时候policy的很小变化可能会导致reward的极大变化，因此我们认为是不合理的。另一种度量是empirical payoffs，通过面对不同对手时的反应揭示策略的潜在不同行为。

+ We formulate the concept of behavioral diversity in the state-action space as the discrepancies of occupancy measures and analyze the optimization methods in both normal-form
games and general Markov games.
+ We provide a new geometric perspective on the response diversity as a form of Euclidean
projection onto the convex hull of the meta-game to enlarge the gamescape directly and
propose the optimization lower bound for practical implementation.
+ We analyze the limitation of exploitability as the evaluation metric and introduce a new
metric with theoretical soundness called population effectivity, which is a fairer way to
represent the effectiveness of a population than exploitability [15]