**1-2 深度分析在在半监督学习中的GCN**

[TOC]

---

# 概要
| |  |
| --- | --- |
| **题目** | Deeper Insights into Graph Convolutional Networks for Semi-Supervised Learning |
| **来源** | AAAI 2018 |
| **作者** |  |
| **摘要** | GCN在卷积层中很好地聚合了局部结点特征和图拓扑结构信息，但它的机制并不清楚（类似一个黑匣子），它仍然需要大量的标记数据来验证和选择模型。<br />在本文中，我们对GCN模型进行了深入的研究，并指出了它的基本的局限性。<br />首先，我们 证明了GCN模型的图卷积实际上是拉普拉斯平滑的一种特殊形式，这是GCN为什么能工作的关键原因，但它也带来了多层卷积层的过平滑问题。<br />其次，为了克服浅层结构GCN模型的局限性，我们提出了训练生成GCN的联合训练和自训练（co-training and self-training）两种方法。我们的方法极大地提高了GCN的学习能力，并且使它们不需要额外的标签进行验证。 |
| **评价** |  |
| **参考笔记** |  |

<a name="RqZ57"></a>
## Introduction （思路整理）
<a name="zCNBi"></a>
#### 
<a name="HP1DO"></a>
## PRELIMINARIES （准备工作）

<a name="mf4DK"></a>
## METHODOLOGY（）

<a name="UKgZ0"></a>
## EXPERIMENTS（）

<a name="NRfzd"></a>
## 引用



