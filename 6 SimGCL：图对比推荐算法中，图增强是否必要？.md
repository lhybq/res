**6 SimGCL：图对比推荐算法中，图增强是否必要？**

[TOC]

---

# 概要
| | SimGCL |
| --- | --- |
| **题目** | Are Graph Augmentations Necessary? Simple Graph Contrastive Learning for Recommendation |
| **来源** | SIGIR 2022  |
| **关键** | 对比学习；数据增强 |
| **代码** | [GitHub - Coder-Yu/QRec: QRec: A Python Framework for quick implementation of recommender systems (TensorFlow Based)](https://github.com/Coder-Yu/QRec)</br>[GitHub - Coder-Yu/QRec: QRec: A Python Framework for quick implementation of recommender systems (TensorFlow Based)](https://github.com/Coder-Yu/QRec) |
| **作者** | Junliang Yu, Hongzhi Yin, Xin Xia, Tong Chen, Lizhen Cui, Nguyen Quoc Viet Hung</br>The University of Queensland, Shandong University, Griffith University |
| **摘要** | 对比学习(CL)**从原始数据中提取自我监督信号的能力与推荐系统解决数据稀疏问题的需求很好地一致**。典型的基于CL的推荐模型是**先用结构扰动对用户项目二部图进行增强**，然后**最大化不同增强图之间的节点表示一致性**。**尽管这些方法被证明是有效的，但性能提升的根本原因仍然是一个谜。【动机之一】**在本文中，首先通过实验揭示，在基于CL的推荐模型中，**CL通过学习更均匀分布的用户/项目表示来工作，这可以隐式地减轻流行度偏差**。同时本文**揭示了被认为是必要的图增强只有微不足道的作用**。基于这一发现，提出了一种简单的CL方法SimGCL，该方法**放弃了图的增强**，而是在**嵌入空间中添加一致的噪声来创建对比视图**。对三个基准数据集的综合实验研究表明，尽管该方法看起来非常简单，但它可以平滑地调整学习表示的**一致性**，并且在推荐准确性和训练效率方面比基于图增强的方法有明显的优势。 |
| **评价** |  |
| **笔记** | [SIGIR 2022 ｜图对比推荐算法中，图增强是否有必要？](https://zhuanlan.zhihu.com/p/549092889)</br>[SIGIR′22 &#124; 推荐系统：图对比学习视图生成新思路Are Graph Augmentations Necessary? Simple Graph Contrastive Learning for](https://zhuanlan.zhihu.com/p/499483845)</br>[论文阅读 Are Graph Augmentations Necessary? Simple Graph Contrastive Learning for Recommendation_怼怼是酷盖的博客-CSDN博客](https://blog.csdn.net/qq_43431934/article/details/124241811)</br>[SIGIR′22 Are Graph Augmentations Necessary? Simple Graph Contrastive Learning for Recommendation](https://www.jianshu.com/p/6544ff5eedc6) |
| **模型框架** |  || 
 |  |


<a name="swzQ4"></a>
# 1.动机motivation

<a name="E55oH"></a>
# 2.相关工作

<a name="NduBx"></a>
# 3.研究方法
<a name="LHjcL"></a>
### 
<a name="tH3d7"></a>
# 4.实验

<a name="Kqprs"></a>
# 5.总结

