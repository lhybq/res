**9 GraphDA：面向推荐的图协同信号去噪与增强**

[TOC]

---

# 概要
| | GraphDA |
| --- | --- |
| **题目** | Graph Collaborative Signals Denoising and Augmentation for Recommendation |
| **来源** | SIGIR 2023 |
| **关键** | 协同过滤，去噪，增强，图推荐 |
| **代码** | [https://github.com/zfan20/GraphDA](https://github.com/zfan20/GraphDA) 没找到代码 |
| **作者** | ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682753900890-52651ba3-5c14-4d23-a7d2-e93838c83f8c.png#averageHue=%23f9f7f6&clientId=ucd886095-798a-4&from=paste&height=178&id=ua34c7128&originHeight=223&originWidth=893&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=39679&status=done&style=none&taskId=uce9ff444-ddd6-4c14-b0d5-24f93316bf3&title=&width=714.4) |
| **摘要** | 图协同过滤(GCF)是推荐系统中用于捕获高阶协同信号的一种流行技术。</br>**动机：**然而，GCF的二部邻接矩阵(基于用户-项目交互的领域聚合)对于**交互丰富的用户/项目可能是有噪声的**，而**对于交互稀缺的用户/项目则是不充分**的。此外，邻接矩阵忽略了**用户-用户**和**项-项相关性**，这可能会限制聚合的有益邻居的范围。**想法：**在这项工作中，我们提出了一个**新的图邻接矩阵**，它结合了用户-用户和项目-项目相关性，以及一个适当设计的用户-项目交互矩阵，它平衡了所有用户之间的交互数量。</br>**方法：** 为了实现这一点，我们**预训练了一种基于图的推荐方法来获得用户/项目嵌入，然后通过top-K采样来增强用户/项目交互矩阵(即改进的邻接矩阵)。**我们还将对称的user-user和item-item相关分量扩展到邻接矩阵。</br>**实验：** 我们的实验表明，**增强的用户-项目交互矩阵具有改进的邻居和更低的密度，在基于图的推荐中有显著的好处。** 此外，**包含用户-用户和物品-物品相关性可以改善对交互丰富和不充分的用户的推荐。** |
| **评价** | 本文提出包含了**用户-用户**和**物品-物品**的**新式图邻接矩阵**，利用其进行信号**去噪**和**增强**；将用户-用户，物品-物品信息加入到原来的邻接矩阵中，不仅可以降噪，还可以缓解对低交互量的用户的信息不充分的问题；> 本文提出的grapDA算法对基于图的推荐效果有显著的帮助，特别是对高度活跃用户和不活跃用户，它们分别需要去噪和增强。|
| **笔记** | None |
| **模型框架** | ![38d77a9929029bcf0cd4bdebdac04946_2_Figure_3_989912942.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682759735114-c814eb6a-8f88-4d77-ac17-a158b8668511.png#averageHue=%23f1edec&clientId=ucd886095-798a-4&from=ui&height=387&id=u811aee9d&originHeight=471&originWidth=714&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=86961&status=done&style=none&taskId=uf9ae8aba-cb29-4b0d-af64-05f2d1ad11c&title=&width=587.3806762695312) |

*** 

# 1.动机motivation
> _基于图的推荐系统使用邻域信息来推断用户/项目嵌入，其中邻接矩阵定义了邻域结构。高阶协同信号通常采用多层叠加的方式进行聚合[3,7,13,19,20,23,30,33,35]。然而，邻域信息的质量取决于邻接矩阵的定义。广泛采用的邻接矩阵是建立在用户-项目交互矩阵之上的，它可能会遇到噪声[10,31,32]、稀疏性[2,11,12,22]、偏差[1,6,38]和长尾[9,26,28]等问题。_

![38d77a9929029bcf0cd4bdebdac04946_1_Figure_1_1771946719.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682760454273-92b21a88-9eeb-4e27-b9ea-7aee860b1a3f.png#averageHue=%23f5f5f5&clientId=ucd886095-798a-4&from=ui&id=ud60a11d6&originHeight=312&originWidth=699&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=31263&status=done&style=none&taskId=ud8f362f3-e2d3-448b-8f54-183ebddfcb6&title=)
两个图模型LightGCN[13]和GTN[10]在Amazon Beauty数据集上
用户/商品的交互量分布(条)和对应的NCDG@5(线)。
如上图，**可以看到用户和物品都遵循长尾分布，大多数用户/物品的交互是有限的**。此外，与直觉相反的现象是，**与交互较少的用户(即不活跃用户)相比，具有丰富交互的用户(即活跃用户)的建模很差[17]**。可以说，其根本原因是**高度活跃的用户有大量的嘈杂交互**，这甚至可能对用户偏好建模有害。此外，当图模型**叠加更多的图卷积层时，会引入更多的噪声**。从项目方面来看，我们可以观察到交互有限的项目执行得并不令人满意。
**基于这些观察，我们认为当前基于图的推荐系统中二部邻接矩阵的定义是不充分的。**
![38d77a9929029bcf0cd4bdebdac04946_1_Figure_2_-210898921.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682760743454-968b8998-0614-41a6-aa57-ef8cfb9e0009.png#averageHue=%23f2f1f1&clientId=ucd886095-798a-4&from=ui&id=u1ef080eb&originHeight=348&originWidth=624&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=49565&status=done&style=none&taskId=u237c8ed0-76c9-425d-b033-aa49f879515&title=)
如上图所示，**直接从用户-物品交互矩阵R构造二部邻接矩阵A来定义用户/物品的邻接结构**。然而，R受到嘈杂和稀疏交互的影响，使得它**不足以表示不活跃的用户/项目**。此外，二部邻接矩阵A**忽略了邻域定义中的user-user[21,37]和item-item相关性[8,14,27]**，即使在增强解[5,34]中也是如此。尽管高阶协作信号可以通过多跳消息传递揭示这些相关性，但最近的研究表明，**长距离消息传递可能会产生新的学习问题，并导致次优表示[4]。**
因此提出了一种新的邻接矩阵设计来改进基于图的推荐。
<a name="E55oH"></a>
# 2.相关工作

***

<a name="NduBx"></a>
# 3.研究方法
我们提出了一个预训练和增强的框架，即GraphDA，来对用户项矩阵进行去噪和增强。
在GraphDA中，我们在GCF的二部邻接矩阵中捕获用户-用户和item-item相关性。具体来说，我们首先对编码器进行预训练，以从现有的用户-项目交互中生成用户/项目嵌入。通过预训练的嵌入，**我们采用top-K采样过程来生成去噪和增强的用户-项目矩阵、非零用户-用户和项目-项目相关性。**
**本文采用lightGCN的嵌入更新公式。**
<a name="AAZhg"></a>
## 3.1用户/项目嵌入的预训练
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682858368566-b09c7ccf-badb-4523-bede-5fc34c806544.png#averageHue=%23f6f5f4&clientId=u351772c4-97f7-4&from=paste&height=57&id=ud69dd49e&originHeight=94&originWidth=554&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=8933&status=done&style=none&taskId=ue6177a6b-6ff6-41e7-a604-55bdec569e3&title=&width=338.6363525390625)使用BPR损失进行预训练。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682858790078-344bba77-f5b0-4a26-a7e4-f2b2471d3fd0.png#averageHue=%23eecaa9&clientId=u351772c4-97f7-4&from=paste&height=244&id=u4fda5fc3&originHeight=348&originWidth=595&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=85932&status=done&style=none&taskId=ucd24fe25-8571-4afc-890a-6b5cc78c0f8&title=&width=417.90338134765625)
<a name="C8u9E"></a>
## 3.2二部图增强
预训练编码了用户-项目协作信号。然而，它在GCF中被当做关键组成部分，即邻接矩阵A，对于用户/项目嵌入学习来说可能不太令人满意，**可能由于观察到有偏差交互作为长尾分布，活跃用户的噪声交互，以及忽略直接的用户-用户和项目-项目相关性**。具体来说，本文在三个分量中增强邻接矩阵，如下图的中心分量所示
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682858752928-99003c61-05b4-4445-829d-33bffc48824b.png#averageHue=%23eecaa9&clientId=u351772c4-97f7-4&from=paste&height=258&id=uf0c99251&originHeight=357&originWidth=603&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=85617&status=done&style=none&taskId=u5fafbc68-db28-4932-8ec0-4ffec39045f&title=&width=435.18182373046875)
<a name="ImBWC"></a>
### User-item Interactions Enhanced.
用预训练的嵌入为用户-项目生成top-k的邻居，定义一个超参数k，用户端可解释为前k隔邻居定义用户的兴趣，商品端解释为k个邻居定义了一个销售群体，k即控制这个邻居的数量。**在k值固定的情况下，实现了对活跃用户的去噪，对不活跃用户的增强作用。** 例如对用户来说，它的k个邻居是通过选择![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682859306077-396efbe5-edfc-4588-aa13-5e38046f0b34.png#averageHue=%23f2ede9&clientId=u351772c4-97f7-4&from=paste&height=34&id=u705c51fb&originHeight=37&originWidth=73&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=1685&status=done&style=none&taskId=u6b2655b7-5279-482d-a12b-00734911d69&title=&width=66.36363492524332)最大的前k个实现**【即从嵌入的角度，选择与用户最相似的k的物品】。**其中![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682859447391-103778da-087f-4402-a256-aa3156263486.png#averageHue=%23f2ede9&clientId=u351772c4-97f7-4&from=paste&height=21&id=ud003d625&originHeight=37&originWidth=73&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=1685&status=done&style=none&taskId=ubc393786-1369-4ad8-a2c9-73c0f78ecb1&title=&width=41)表示预训练得到的物品嵌入。类似地，可以从物品端进行增强。
<a name="qqk0J"></a>
### User-User and Item-Item Correlations.
传统的邻接矩阵![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682859707823-79ca9678-4dbb-4a81-9468-73b91e50997d.png#averageHue=%23e9e6e4&clientId=u351772c4-97f7-4&from=paste&height=36&id=u6bf92cb9&originHeight=40&originWidth=104&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=1171&status=done&style=none&taskId=uf9471ef9-8f2e-4d89-b4ff-6348825151e&title=&width=94.54545249623705)存在主对角上的零矩阵，本文提出用$W_{UU}$ 和$W_{II}$填充这两个零矩阵，从而引入项目-项目，用户-用户信息，进一步增强矩阵。使用![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682859962881-fe16b15a-150e-41c0-aaf3-0dab934b2084.png#averageHue=%23f7f5f2&clientId=u351772c4-97f7-4&from=paste&height=56&id=ub07117e6&originHeight=62&originWidth=247&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=5289&status=done&style=none&taskId=u7d979874-adc2-4ebf-9075-de8b5baaa98&title=&width=224.545449678563)获得用户/物品的相似用户，![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682860053846-60d77be3-4fc5-49c2-b946-948120dbdd2e.png#averageHue=%23ede7e1&clientId=u351772c4-97f7-4&from=paste&height=37&id=u0a89167f&originHeight=41&originWidth=42&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=1275&status=done&style=none&taskId=u65b8f3b6-6494-4b15-b5d8-36d5e9e0db9&title=&width=38.18181735424958)表示预训练的用户嵌入，如果u与uk认为相似，则有$W_{UU}[u,u_k]=1$，且有$W_{UU}, W_{II}$是对称的。
<a name="xXDgg"></a>
### GCF Re-learn with Enhanced Bipartite Adjacency.
使用增强的图邻接矩阵，重新学习图编码器(即随机初始化)以生成用于用户-项目交互预测的嵌入。
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682860463963-8edb8736-71c6-496c-bfc0-6f22eb6e601d.png#averageHue=%23f4f1ee&clientId=u351772c4-97f7-4&from=paste&height=174&id=uaff7157b&originHeight=249&originWidth=614&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=48892&status=done&style=none&taskId=u25715ff2-c760-473f-9977-f1caa5db6a8&title=&width=429.18182373046875)
<a name="tH3d7"></a>
# 4.实验
**数据集：**使用公开的亚马逊评论数据集[25]和三个基准类别[10,13,18,30]，
(1)Beauty有22,363个用户，12,101个商品，198,502次交互，密度为0.05%;
(2)玩具游戏类(Toys)用户19,412人，商品11924件，互动167,597次，密度0.07%;
(3) Tools and Home (Tools)拥有16,638个用户，10,217个项目，134,476次交互，密度为0.08%。
本文遵循5核心设置，将评论的存在视为积极表现。![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682863169163-2308f3ad-cc1d-40ca-8a41-081e77fe7e58.png#averageHue=%23efeae5&clientId=u351772c4-97f7-4&from=paste&height=72&id=uf7e0bca8&originHeight=79&originWidth=615&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=20940&status=done&style=none&taskId=u7d2c1027-e7e8-4f7d-92bf-0fac4555bf8&title=&width=559.0908969729403)
我们按时间顺序对每个用户的交互进行排序，并采用“留一个”设置，最后一个交互项用于测试，最后第二个交互项用于验证。

**参数设置：**
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682863255651-1958672f-a0c2-485d-9d25-59686fb15411.png#averageHue=%23f1ece8&clientId=u351772c4-97f7-4&from=paste&height=252&id=uce9be8ce&originHeight=277&originWidth=634&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=69315&status=done&style=none&taskId=uc305a2f8-24c3-4ef4-9fb3-50f7b79f03d&title=&width=576.3636238712912)
![38d77a9929029bcf0cd4bdebdac04946_3_Table_1_1094189694.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1682863030592-da742942-0ca5-4d26-a01d-ce92a59571c7.png#averageHue=%23e4e4e4&clientId=u351772c4-97f7-4&from=ui&id=ud32bbd8f&originHeight=273&originWidth=1506&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=79710&status=done&style=none&taskId=u0d10939a-207a-4da9-988e-733a88239e8&title=)
<a name="Kqprs"></a>
# 5.总结
GraphDA增强的邻居在基于图的推荐中显着使非活跃用户受益。
GraphDA的去噪交互改善了具有噪声交互的高度活跃用户，特别是在具有潜在更多噪声的数据集中。
> 作者总结：我们实证地研究了基于图的推荐存在的不足，并且可以说，问题来自于对二部邻接矩阵的不满意的定义。为了生成更好的二部邻接矩阵，我们提出了带有预训练和增强步骤的去噪和增强管道GraphDA，以生成更好的用户-项矩阵、用户-用户相关性以及项-项相关性。实验证明了GraphDA的优越性，特别是对于高度活跃的用户和不活跃的用户。

