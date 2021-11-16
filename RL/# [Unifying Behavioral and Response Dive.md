# [Unifying Behavioral and Response Diversity for Open-ended Learning in Zero-sum Games](https://arxiv.org/pdf/2106.04958.pdf)

## introduction
policy diversity是面对 non-transitive dynamics(存在strategic cycle，例如石头剪刀布)的一个重要问题。其中维护一个diverse policies pool via open-ended learning是一个可能的解决方案。  
但是现在没有很好的diversity的定义，因此也很难研究。这里总结了过去diversity的概念，提供了a unified measure of diversity in multi-agent open-ended learning，基于behavioral diversity和response diversity。  
+ At the trajectory distribution level, we re-define BD in the state-action space as the discrepancies of occupancy measures.  
+ For the reward dynamics, we propose RD to characterize diversity through the responses of policies when encountering different opponents. 
现在的方法没有兼顾两者的。  
在这种度量下，我们设计了一种diversity-promoting objective and population effectivity in open-ended learning。