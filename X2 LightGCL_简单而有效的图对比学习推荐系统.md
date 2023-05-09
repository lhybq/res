**1 GDE_Less is more，更轻量化的图卷积推荐系统**

[TOC]

---

# 概要
| | LightGCL |
| --- | --- |
| **题目** | LightGCL: Simple Yet Effective Graph Contrastive Learning for Recommendation |
| **来源** | **ICLR 2023** |
| **代码** | https://github.com/HKUDS/LightGCL |
| **关键** | GCL; SVD; 对比学习 |
| **作者** | Xuheng Cai;Chao Huang∗ ;Lianghao Xia;Xubin Ren<br />Department of Computer Science, University of Hong Kong |
| **摘要** | 图形神经网络（GNN）是基于图形的推荐系统的一种强大的学习方法。最近与对比学习相结合的GNN通过其数据增强方案在推荐方面表现出了优异的性能，旨在处理高度稀疏的数据。尽管取得了成功，但大多数现有的图对比学习方法要么对用户项目交互图执行随机增强（例如，节点/边缘扰动），要么依赖基于启发式的增强技术（例如，用户聚类）来生成对比视图。我们认为，这些方法不能很好地保持固有的语义结构，并且容易受到噪声扰动的影响。在本文中，我们提出了一种简单而有效的图形对比学习范式LightGCL，该范式缓解了这些损害基于CL的推荐器的通用性和鲁棒性的问题。该模型专门利用奇异值分解进行对比增强，从而实现了全局协作关系建模的无约束结构细化。在多个基准数据集上进行的实验表明，与现有技术相比，我们的模型的性能显著提高。进一步的分析证明了LightGCL对数据稀疏性和流行性偏差的鲁棒性的优势。 |
| **评价** | 一种对比学习范式 LightGCL 用于图增强。**奇异值分解** (SVD) 指导，**注入全局协作关系**，**反映用户独特的偏好和跨用户的全局依赖关系，**模型可减轻对比信号不准确带来的问题。由于本文使用奇异值分解，提取交互矩阵隐藏的全局信息，<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678109626913-9d692e33-f25c-48d4-9d9c-4910e89e2458.png#averageHue=%23252321&clientId=u8b985a29-cb88-4&from=paste&height=174&id=u956f56e1&originHeight=191&originWidth=1064&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=71364&status=done&style=none&taskId=ue9ea5295-c5ce-4349-adec-7fadbe217a8&title=&width=967.272706307656) |
| **笔记** |[笔记1](https://blog.csdn.net/weixin_41278720/article/details/127642767)</br>[笔记2](https://mp.weixin.qq.com/s/kn0ycWediqAPC--glzGKDg) </br> [相关资料1](https://zhuanlan.zhihu.com/p/346686467)</br> [相关资料2](https://zhuanlan.zhihu.com/p/172254089) </br>[相关资料3](https://zhuanlan.zhihu.com/p/29846048)</br> |
| **模型框架** | ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678108540857-ad9854dc-37c7-4064-b074-80f05879b04a.png#averageHue=%23faf7f5&clientId=u8b985a29-cb88-4&from=paste&height=470&id=u982c5b72&originHeight=517&originWidth=845&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=104821&status=done&style=none&taskId=u1addb762-cfd9-4d40-a4da-3f9129c494b&title=&width=768.181801531926) |
|  | ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678114927858-de824dae-5938-4d81-b14e-8e915c52c5ac.png#averageHue=%23fcfbf8&clientId=u5ee19083-9cbf-4&from=paste&height=470&id=u206f5b5d&originHeight=517&originWidth=654&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=79723&status=done&style=none&taskId=u629c4992-84d3-485d-b6c3-3ed671051cd&title=&width=594.5454416590292)<br /> [一些质疑](https://zhuanlan.zhihu.com/p/614203294/ )|

***

<a name="swzQ4"></a>
# 1.动机

在实际推荐场景中，在有限的交互数据中学习高质量的用户和项目表示时会面临数据稀疏问题。因此，对比学习被引入推荐任务以进行数据增强。
当前**大多数图对比学习 (GCL)方法采用基于启发式的对比视图生成器来最大化输入正对之间的互信息并推开负例**。为了构建扰动视图，SGL通过使用随机增强策略 (例如节点丢弃和边扰动)破坏用户-项目交互图的结构信息来生成正视图的节点对。SimGCL提供了带有随机噪声扰动的嵌入增强。为了识别节点 (用户和项目)的语义邻居， HCCF和NCL追求图结构相邻节点和语义邻居之间的一致表示。
但最先进的对比学习推荐系统仍存在几个固有的局限性：
   1. 带有随机扰动的图增强可能会**丢失有用的结构信息**，从而误导对比表示学习； 
   2. 启发式引导表示对比方案的成功很大程度上建立在视图生成器上，这**限制了模型的通用性并且容易受到嘈杂的用户行为数据的影响**； 
   3. 当前大多数基于GNN的对比推荐器都**受到过平滑问题的限制**，导致无法区分表示。
鉴于以上缺陷，本文提出简单而有效的增强方法 LightGCL 重新审视图对比学习范式进行推荐。 <br />在该模型中，图增强由**奇异值分解** (SVD) 指导，不仅提炼用户-项目交互的有用信息，而且还将全局协作上下文注入对比学习的表示对齐中。 **强大的图对比学习范式可以很好地保留用户-项目交互的重要语义，而不是生成两个手工制作的增强视图，这使我们的自增强表示 (self-augmented representations)能够反映用户独特的偏好和跨用户的全局依赖关系。**
<a name="E55oH"></a>
***
# 2.相关工作
<a name="tQKAR"></a>
## Graph Contrastive Learning for Recommendation. 
<a name="sP0Wp"></a>
## Self-Supervised Learning on Graphs.
<a name="NduBx"></a>

***

# 3.研究方法
通过全局协作关系来增强图对比学习，减小图对比学习对图结构的破坏。
![d3977a29bf71f82e20699a5cdd987741_2_Figure_1_-572113793.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678113309584-372ddeb5-8de1-4df1-87bb-daddf9e7da2a.png#averageHue=%23f4f1ef&clientId=u5ee19083-9cbf-4&from=ui&id=u6e1f0b27&originHeight=459&originWidth=885&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=85453&status=done&style=none&taskId=ud6b8e7ad-c5e5-401a-a7f7-6ccff840c5d&title=)<br />图上半部分作为GCN主干，提取局部图依赖关系，svd引导的增强图下半部分，使图对比学习具有全局协作关系分析，以学习有效的用户和项目表示
<a name="kLHaj"></a>
## 3.1局部图依赖关系建模
与通常的协同过滤范式相类似，每个用户和物品都具有一个可学习的隐式表征向量，以表示结点的特征。LightGCL采取了常用的双层图卷积网络（Graph Convolutional Network，或简称 GCN），对用户与物品间局部的领域关系进行学习。在每一层图卷积网络中，每个结点的表征向量都会依着图的边传播到相邻结点。<br />**为了防止过拟合，LightGCL 采用了剩余连接（residual connection）的方法，使每个结点在信息传播和整合中不至于丧失自身的有效信息。**<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678112689401-997b03b2-d61c-4e2b-bd59-e5b21ec30166.png#averageHue=%23efece9&clientId=u8b985a29-cb88-4&from=paste&height=185&id=u635ee6f6&originHeight=204&originWidth=143&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=9773&status=done&style=none&taskId=ub6c8790a-5447-4b65-92d8-8dac00f9bdd&title=&width=129.99999718232596)
<a name="YFcU2"></a>
## 3.2基于奇异值分解的全局信息提取
LightGCL 采用了**奇异值分解重构的数据增强方法来探索图中隐藏的信息**。具体而言，LightGCL 首先对用户-物品交互矩阵进行奇异值分解，得到两个包含交互矩阵行与行之间相关量矩阵的特征值向量的低阶矩阵，以及一个对角线上包含交互矩阵的奇异值的对角矩阵。<br />将奇异值由大到小排列，**越大的奇异值往往对应着矩阵中重要的组成成分**，截取最大的 q 个奇异值，抛弃剩余的较小奇异值，并重构邻接矩阵。<br />这个重构的邻接矩阵实际上是原交互矩阵的低阶近似，不仅包含了原邻接矩阵中的重要组分信息，而且由于其为全连接图，考虑了每一对用户和物品之间的潜在关联，更能挖掘图中的全局信息。鉴于以上的优点，LightGCL 采用了这个重构的邻接矩阵作为对比学习中的增强图。<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678112952764-736e9ad1-b967-4e1a-b34f-f51232ef8164.png#averageHue=%23f6f1eb&clientId=u5ee19083-9cbf-4&from=paste&height=367&id=uefb3227a&originHeight=404&originWidth=170&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=28341&status=done&style=none&taskId=u1deddc28-3e68-45d9-871c-893598ca20c&title=&width=154.5454511957721)
<a name="t7kxZ"></a>
## 3.3简单的对比学习
传统的图对比学习方法（如 SGL，SimGCL 等）需要创建两个增强图？，而在原图上产生的表征向量并不参与对比学习。这些模型之所以采用这种低效的结构，可能是因为他们采用的基于随机过程的图增强方法可能对主任务的学习起到误导效果。（理解为随机增强信息和奇异值分解方法相比，对主要的学习任务产生负面影响，因为随机增强具有盲目性）<br />在本文提出的 LightGCL 框架中，增强图实际上包含奇异值分解带来的有效信息，可以加强主任务的学习。因此，我们得以将奇异值分解重构图产生的表征向量于原图产生的表征向量直接进行对比学习。这样一来，模型只需计算一个增强图，大大简化了对比学习的范式。而且，奇异值分解重构的图可以由低阶矩阵表示，而低阶矩阵大大提高了矩阵乘法的效率。<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678113180535-0b396980-4fc6-4d7c-86d0-686c347a9af6.png#averageHue=%23f3f2f0&clientId=u5ee19083-9cbf-4&from=paste&height=225&id=u7e5be327&originHeight=248&originWidth=1034&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=45578&status=done&style=none&taskId=u03387a32-22fa-4868-9683-98d90952459&title=&width=939.9999796260491)
<a name="tH3d7"></a>
***
# 4.实验
为了验证所提出的LightGCL方法的优越性和有效性，文章进行了大量的实验来回答以下研究问题:
> •RQ1:与各种SOTA基线相比，LightGCL在不同数据集上的表现如何?
> •RQ2:轻量级图对比学习如何提高模型效率?
> •RQ3:我们的模型如何应对数据稀疏性、流行度偏差和过度平滑?
> •RQ4:局部全局对比学习对我们模型的性能有何贡献?
> •RQ5:不同的参数设置如何影响我们的模型性能?

<a name="Kqprs"></a>
# 5.总结
> 探索了使奇异值分解强大到足以增强用户-项目交互图结构的关键思想。
> 研究结果表明，本文图增强方案在抵抗数据稀疏性和流行度偏差方面表现出很强的能力。
> 大量的实验表明，本文模型在几个公共评估数据集上取得了新的最先进的结果。


