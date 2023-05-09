**1 GDE_Less is more，更轻量化的图卷积推荐系统**

[TOC]

---

# 概要
| | CLCF |
| --- | --- |
| **题目** | On the Vulnerability of Graph Learning based Collaborative Filtering |
| **来源** | **TOIS 2022** (ACM Transactions on Information Systems, CCF A)  |
| **关键** | 协同过滤；图神经网络；脆弱性 |
| **代码** |  |
| **作者** | ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678797366887-58404e37-2145-40a7-8f06-ea80dcecf4f5.png#averageHue=%23f4f1ee&clientId=u0fdeaeb1-3286-4&from=paste&height=188&id=u5c27c62e&originHeight=259&originWidth=934&originalType=binary&ratio=1.375&rotation=0&showTitle=false&size=67448&status=done&style=none&taskId=uc9be6834-e962-4044-8804-0d7345904c1&title=&width=679.2727272727273) |
| **摘要** | **基于图学习的协同过滤(GLCF)建立在图神经网络(GNNs)消息传递机制的基础上**，受到了广泛关注，在推荐系统中表现出了优越的性能。然而，尽管已经有工作表明 GNN 很容易被对抗性攻击破坏，但依旧很少关注 GLCF 的**脆弱性**。如GLCF模型的很容易被欺骗，因为对GNN的探索还远远不够。本文中，我们建议研究 GLCF 的脆弱性。具体而言，**首先针对 CLCF 提出了一种对抗性攻击**。考虑到攻击GLCF的独特挑战，我们建议在**搜索局部最优扰动时采用贪心策略**，设计合理的攻击效用函数来处理不可参考的排名导向指标。**接下来提出一种鲁棒GCLF的防御**。防御基于这样一种观察，即**攻击通常会在图中引入可疑交互，从而操纵消息传递过程**。然后，我们建议测量每个交互的可疑分数并进一步减少可疑交互的消息权重。本文还给出了其稳定性的理论保证。最后三个基准数据集的实验结果表明我们的攻击和防御的有效性。 |
| **评价** | |
| **笔记** | |
| **模型框架** |  |


# 1.动机

# 2.相关工作

# 3.研究方法

# 4.实验

# 5.总结
>


