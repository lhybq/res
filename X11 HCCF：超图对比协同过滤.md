
**X11 HCCF：超图对比协同过滤**

[TOC]

---

# 概要
| | HCCF |
| --- | --- |
| **题目** | Hypergraph Contrastive Collaborative Filtering |
| **来源** | SIGIR 2022 |
| **关键** | 超图；对比学习； |
| **代码** | [GitHub - akaxlh/HCCF: HCCF, SIGIR 2022](https://github.com/akaxlh/HCCF) |
| **作者** | ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683007632047-077e5a17-5975-4565-9905-645e975e39e9.png#averageHue=%23fdfcfa&clientId=uee43972b-69c3-4&from=paste&height=198&id=u071fe02b&originHeight=218&originWidth=1172&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=57234&status=done&style=none&taskId=u2781cb78-ed0c-4bc1-a34e-b616f5f604d&title=&width=1065.4545223614407)香港大学**黄超**实验室 |
| **摘要** | 协同过滤(CF)已经成为将用户和项目参数化到潜在表示空间的基本范式，并从交互数据中获取它们的相关模式。在各种CF技术中，基于gnn的推荐系统(如PinSage和LightGCN)的开发提供了最先进的性能。然而，在现有的解决方案中，有两个关键的挑战没有得到很好的探讨:</br>**动机：** i)基于基于图的深层次的CF架构的**过度平滑效应**，可能导致用户表示无法区分（同质化）和推荐结果的退化。ii)监督信号(即用户-物品交互)在现实中通常是**稀缺的，分布有偏差的**，这限制了CF范式的表示能力。</br>**方法：**为了解决这些挑战，我们提出了一个新的自监督推荐框架-超图对比协同过滤(HCCF)，通过**超图增强的跨视图对比学习架构来共同捕获局部和全局协作关系**。特别地，设计的超图学习结构增强了基于gnn的CF范式的判别能力，从而全面捕获用户之间复杂的高阶依赖关系。此外，我们的HCCF模型基于超图增强的自我区分，有效地将**超图结构编码**与**自监督学习结合**起来，增强推荐系统的表示质量。【超图+对比学习】</br>在三个基准数据集上的大量实验表明，我们的模型优于各种最先进的推荐方法，并且对稀疏的用户交互数据具有鲁棒性。 |
| **评价** |- **局部协同关系编码**，这部分是利用常见的方法如LightGCN这类方法进行信息传递和聚合，并且在聚合的时候利用残差的方式连接前一层和当前层的计算结果，缓解过度平滑</br>- **全局依赖关系**，这部分采用超图结构，</br>**首先**，超图中节点对应的超边矩阵是可学习的，因此超图的结构是学习得到的；</br>**其次**，为了避免直接学习上述超边矩阵这样一个稠密的大矩阵，作者对其进行了分解，由节点embedding和参数W计算，有点类似于FM中的分解；</br>**然后**，分层映射部分，是在多层超图消息传播的过程中对超边也进行交互，即整个传播过程中，通过超图结构聚合节点embedding，当前层节点的embedding是由局部协同部分得到，并且交互超边embedding；</br>- 聚合多阶消息传播得到的用户/商品局部embedding和超图embedding，进行预测，利用pairwise损失计算损失值</br>- 对比学习阶段是约束每一层超图得到的embedding和局部协同部分得到的embedding的一致性，从而得到较好的表征</br>- 在原始图结构和超图结构中引入边dropout防止过拟合|
| **笔记** |  |
| **模型框架** | ![457e53fc3b6356afa4685d3f38671326_2_Figure_2_1815174249.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683015889654-125f0b91-5717-4cb7-8ed6-1dd52cbf1147.png#averageHue=%23f5f4f2&clientId=uee43972b-69c3-4&from=ui&id=u773be7b9&originHeight=546&originWidth=723&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=101065&status=done&style=none&taskId=u57ad6e7d-9c3e-4b9d-bda4-2575bea5a03&title=) |
| **问题** | [【图协同过滤】对顶会论文HCCF以及LightGCL模型的一些质疑](https://zhuanlan.zhihu.com/p/614203294/)</br>[SimGCL在论文中处于underfitting的状态 · Issue #6 · HKUDS/LightGCL](https://github.com/HKUDS/LightGCL/issues/6) |

**有坑，，快跑**
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

