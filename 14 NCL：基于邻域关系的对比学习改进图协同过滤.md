**14：NCL：基于邻域关系的对比学习改进图协同过滤**

[TOC]

---
# 概要
| | NCL|
| --- | --- |
| **题目** | Improving Graph Collaborative Filtering with Neighborhood-enriched Contrastive Learning Collaborative Filtering  |
| **来源** | WWW 2022 |
| **关键** | 协同过滤；对比学习；领域增强 |
| **代码** | [https://github.com/RUCAIBox/NCL](https://github.com/RUCAIBox/NCL) |
| **作者** | 中国人民大学 zhaoxin<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683020104691-5be5c213-35d1-4e83-a455-97df97e5e07a.png#averageHue=%23f2ede8&clientId=ucf82ab94-8d3e-4&from=paste&height=141&id=ud2d6d3a7&originHeight=141&originWidth=665&originalType=binary&ratio=1&rotation=0&showTitle=false&size=35906&status=done&style=none&taskId=u139b4e18-d07c-4410-9c65-32189bc1876&title=&width=665) |
| **摘要** | 近年来，图协同过滤方法被提出作为一种有效的推荐方法，该方法通过对用户-商品交互图建模来获取用户对商品的偏好。<br />**动机：**尽管这些方法很有效，但在实际场景中存在**数据稀疏**的问题。为了减少数据稀疏性的影响，在图协同过滤中采用**对比学习**来提高性能。然而，这些方法通常采用随机抽样的方式构建对比对，忽略了用户(或商品)之间的**相邻关系**，未能充分发挥对比学习在推荐中的潜力。<br />**思路：**为了解决上述问题，我们提出了一种新的对比学习方法，称为**邻居丰富的对比学习**，命名为**NCL**，**它明确地将潜在邻居纳入对比对**。<br />**方法：**具体地说，我们分别**从图结构和语义空间引入用户(或项目)的邻居**。对于交互图上的结构邻居，我们开发了一个新的结构对比目标，将用户(或项目)及其结构邻居视为积极的对比对。在实现中，用户(或项)和邻居的表示对应于不同GNN层的输出。此外，为了挖掘语义空间中的潜在邻居关系，我们假设具有相似表征的用户在语义邻居范围内，并将这些语义邻居纳入原型对比目标中。<br />所提出的NCL可以用EM算法进行优化，并推广应用于图协同滤波方法。在五个公共数据集上的大量实验证明了所提出的NCL的有效性，特别是在Yelp和amazon图书数据集上，与基础模型相比，性能分别提高了26%和17%。 |
| **评价** |  |
| **笔记** | [Improving Graph Collaborative Filtering with Neighborhood-enriched Contrastive Learning_ZZZ___bj的博客-CSDN博客](https://blog.csdn.net/ZZZ___bj/article/details/123537493)</br>[WWW′22  推荐系统：基于邻域关系的对比学习改进图协同过滤Improving Graph Collaborative Filtering with Neighborhood-enriched](https://zhuanlan.zhihu.com/p/478652789)|
| **模型框架** | ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683103800421-abac77cd-e1b4-45a8-9d41-4ae4857dd36f.png#averageHue=%23eee8e6&clientId=ucf82ab94-8d3e-4&from=paste&height=496&id=u35eacf5c&originHeight=546&originWidth=689&originalType=binary&ratio=1&rotation=0&showTitle=false&size=97373&status=done&style=none&taskId=u2bba23bd-9f8f-4ea4-abb1-7968c496de5&title=&width=626.3636227875704) |
| **问题** | <br /> |

***

# 1.动机motivation &&概要summary
> 现有的神经图协同过滤方法仍然存在两个主要问题。**首先**，用户-物品交互数据通常是稀疏的或有噪声的，它可能无法学习可靠的表示，因为基于图的方法可能更容易受到数据稀疏性的影响。**其次**，现有的基于GNN的CF方法依赖于显式交互链接来学习节点表示，而高阶关系或约束(例如用户或项目相似性)不能显式地用于丰富图信息，这已被证明在推荐任务中非常有用[24,27,35]。尽管最近的一些研究利用对比学习来缓解交互数据的稀疏性[33,39]，但他们通过随机采样节点可能损坏子图来构造对比对。对于如何针对推荐任务构建更有意义的对比学习任务缺乏考虑[24,27,35]。
> 

除了直接的用户-项目交互，存在多种对推荐任务有用的潜在关系（例如，用户相似性），我们旨在设计更有效的恒定学习方法，以在神经图协作过滤中利用这些有用关系。<br />特别地，我们**考虑用户（或项目）的节点级关系**，这比图级关系更有效。我们将这些附加关系描述为节点的丰富邻域，可以在两个方面进行定义：**（1）结构邻居是指通过高阶路径在结构上连接的节点，（2）语义邻居是指在图上可能无法直接到达的语义相似的邻居。**我们的目标是利用这些丰富的节点关系来改进节点表示的学习（即，编码用户偏好或项目特征）。![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683033017204-e97bbb32-0840-4d43-bbf6-302a4f270006.png#averageHue=%23e7e4e2&clientId=ucf82ab94-8d3e-4&from=paste&height=307&id=u01336987&originHeight=338&originWidth=685&originalType=binary&ratio=1&rotation=0&showTitle=false&size=68612&status=done&style=none&taskId=ue85f6d37-90d9-43b2-9b64-59a3b327b44&title=&width=622.7272592300229)<br />如前所述，本文的NCL基于两种**扩展邻居构建节点级对比目标**。我们在图1中对NCL和现有的恒定学习方法进行了比较。然而，节点级对比目标通常需要对每个节点对进行成对学习，这对于大型邻域来说非常耗时。考虑到效率问题，我们为每种邻居学习一个代表性嵌入，从而可以使用两个代表性的嵌入（结构或语义）来完成节点的持续学习。<br />具体来说，对于结构邻居，我们注意到𝑘-GNN的第三层涉及𝑘-跳邻居。因此，我们利用𝑘-来自GNN的第层输出，表示为𝑘-为节点跳邻居。我们设计了一个结构感知的对比学习目标，该目标提取节点（用户或项目）的表示及其结构邻居的代表性嵌入。对于语义邻居，我们设计了一个原型对比学习目标，以捕获节点（用户或项目）与其原型之间的相关性。粗略地说，原型可以被视为表示空间中语义相似的邻居的聚类的质心。由于原型是潜在的，我们进一步建议使用期望最大化（EM）算法[19]来推断原型。通过结合这些附加关系，我们的实验表明，它可以在很大程度上改进原始的基于GNN的内隐反馈推荐方法（也优于现有的恒定学习方法）。我们的贡献可以概括为三个方面：<br />本文提出NCL通用的对比学习框架，它**结合了结构和语义邻居**，以改进神经图协作过滤<br />本文建议两种邻居的代表嵌入，使得在节点和相应的代表嵌入式之间常数学习可以执行，这大大提高了算法效率。
<a name="E55oH"></a>
# 2.相关工作
基于图的协同过滤
<a name="NduBx"></a>
# 3.研究方法
<a name="BJK50"></a>
## 3.2Contrastive Learning with Structural Neighbors
现有的图协同过滤模型主要是用观察到的交互（例如，用户-项目对）来训练的，而用户或项目之间的潜在关系不能通过从观察到的数据中学习来明确地捕捉。**为了充分利用对比学习的优势，我们建议将每个用户（或项目）与他/她的结构邻居进行对比【与邻居进行对比】，**这些邻居的表示是通过GNN的层传播聚合的。<br />形式上，在图协同过滤模型[9]中，用户/项目的初始特征或可学习嵌入可以通过$z^{(0)}$表示。最终输出可以被视为子图中嵌入的组合，该子图包含不同跳处的多个邻居。具体而言，基础GNN模型的第$l-th$处的输出$z^{(l)}$是$l-hop$时的结构上的每个领域邻居节点的加权和，因为传播时没有变换和自循环[9]<br />考虑到交互图G是一个二分图，利用基于GNN的模型在图上进行**偶数次的信息传播**自然地聚集了同质结构邻居的信息，这使得提取用户或物品潜在邻居变得方便**。通过这种方式，我们可以从GNN模型的偶数层（例如，2、4、6）输出中获得同质邻域的表示。**通过这些表示，我们可以有效地建模用户/项目与其同质结构邻居之间的关系。**具体而言，我们将用户自身的嵌入和偶数层GNN的相应输出的嵌入视为正对。**基于InfoNCE[20]，我们提出了结构对比学习目标，以最小化它们之间的距离，如下所示：<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683110647240-50b2aa7e-5b26-4158-b6bf-35797d947024.png#averageHue=%23f3f1f0&clientId=ucf82ab94-8d3e-4&from=paste&height=57&id=u2a805142&originHeight=121&originWidth=666&originalType=binary&ratio=1&rotation=0&showTitle=false&size=18299&status=done&style=none&taskId=uf2465101-eeb6-4f81-889f-fdd936f748f&title=&width=314.4488525390625)![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683110766634-a5f7cae2-060e-4914-81f7-52371518c922.png#averageHue=%23f5f4f2&clientId=ucf82ab94-8d3e-4&from=paste&height=77&id=ub3b82862&originHeight=144&originWidth=638&originalType=binary&ratio=1&rotation=0&showTitle=false&size=17420&status=done&style=none&taskId=u6c78c8c6-0869-42f8-8098-4335715a664&title=&width=342.9942932128906)<br />其中$z_u^{(k)}, z_i^{(k)}$是GNN第k的归一化输出(k为偶数)，完整的损失为![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683110835488-ac9caf38-b7a0-44fa-b869-61d394e8aefb.png#averageHue=%23f1eeed&clientId=ucf82ab94-8d3e-4&from=paste&height=29&id=uee103ab4&originHeight=52&originWidth=255&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4191&status=done&style=none&taskId=ub75e3bd7-5de9-4731-bff9-9f1d7061773&title=&width=142.81817626953125)<br />其中
<a name="ciUNo"></a>
## 3.3Contrastive Learning with Semantic Neighbors
> 结构对比损失显式地挖掘了由交互图定义的邻居。然而，结构对比损失平等地对待用户/项目的同质邻居，这不可避免地将噪声信息引入对比对。为了减少来自结构邻居的噪声的影响，我们考虑通过合并语义邻居来扩展对比对，**语义邻居指的是图上不可达的节点，但具有相似的特征（项目节点）或偏好（用户节点）。**

受先前工作的启发[16]，我们可以通过学习每个用户和项目的潜在原型来识别语义邻居。基于这一思想，我们进一步提出了原型对比目标，以探索潜在的语义邻居，并将其纳入对比学习中，以更好地捕捉协同过滤中用户和项目的语义特征。特别是，相似的用户/项目往往落在相邻的嵌入空间中，原型是表示一组语义邻居的集群的中心。因此，我们将聚类算法应用于用户和项目的嵌入，以获得用户或项目的原型。由于该过程不能进行端到端优化，我们使用EM算法学习了所提出的原型对比目标。形式上，GNN模型的目标是最大化以下对数似然函数：<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683111205707-315a3313-baf7-4f86-972a-79a982df527c.png#averageHue=%23f4f2f0&clientId=ucf82ab94-8d3e-4&from=paste&height=52&id=ufb58d1d2&originHeight=70&originWidth=508&originalType=binary&ratio=1&rotation=0&showTitle=false&size=9275&status=done&style=none&taskId=uf5f8e945-73d4-46f0-a52b-18eae72e9f5&title=&width=379.81817626953125)<br />其中Θ是一组模型参数，R是交互矩阵。𝑐𝑖 是用户𝑢的潜在原型， 类似地，我们可以定义物品的优化目标。之后，提出的原型对比学习目标是在InfoNCE[20]的基础上最小化以下函数：<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683111340020-9008ac05-4feb-4955-827c-d20379e813ca.png#averageHue=%23f2efed&clientId=ucf82ab94-8d3e-4&from=paste&height=62&id=uc51230ae&originHeight=68&originWidth=397&originalType=binary&ratio=1&rotation=0&showTitle=false&size=8717&status=done&style=none&taskId=ud5a2c5ed-6c70-4e35-93f7-27d17740b67&title=&width=360.9090830865972)![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683111351098-23179f45-0880-40cb-83b5-79021866e463.png#averageHue=%23f3f1ef&clientId=ucf82ab94-8d3e-4&from=paste&height=65&id=u1f6c9ef4&originHeight=72&originWidth=385&originalType=binary&ratio=1&rotation=0&showTitle=false&size=8397&status=done&style=none&taskId=u0aa257cb-a404-4399-9e09-93fda68faed&title=&width=349.9999924139545)<br />𝑐𝑖 是用户𝑢 的原型，这是通过对所有用户嵌入进行聚类得到的𝐾-均值算法，使用𝑘个集群覆盖所有用户；物品端类似，完整的损失为：![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683111445960-d05a8397-01b3-48ed-95ce-cdaa559771fb.png#averageHue=%23f2f0ed&clientId=ucf82ab94-8d3e-4&from=paste&height=35&id=u940c9010&originHeight=38&originWidth=186&originalType=binary&ratio=1&rotation=0&showTitle=false&size=2719&status=done&style=none&taskId=uec7379d9-637d-4a86-bac0-0ef01c35680&title=&width=169.0909054259624)
<a name="VEvVM"></a>
## 3.4Optimization
由于协作过滤器的主要目标是对用户和项目之间的交互进行建模，我们将**所提出的两种对比学习损失视为补充**，并利用多任务学习策略来联合训练传统的排名损失和所提出的对比损失：<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683111630921-93b69ddd-832b-42b1-8dd6-e15a68de0028.png#averageHue=%23f2efea&clientId=ucf82ab94-8d3e-4&from=paste&height=30&id=u98ea03d7&originHeight=33&originWidth=389&originalType=binary&ratio=1&rotation=0&showTitle=false&size=4751&status=done&style=none&taskId=ubedbc2bd-7168-4100-9f93-760eddaf369&title=&width=353.63635597150204)<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683111812443-527290eb-b521-42f2-a15b-54d341529a7a.png#averageHue=%23f5f1ed&clientId=ucf82ab94-8d3e-4&from=paste&height=869&id=u34d88a3b&originHeight=612&originWidth=501&originalType=binary&ratio=1&rotation=0&showTitle=false&size=114331&status=done&style=none&taskId=ub4b83886-5baf-490e-a793-48348be2f9f&title=&width=711.4545288085938)
<a name="tH3d7"></a>
# 4.实验
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1683116933784-9995c9ae-a942-4e66-9773-75d6deead8c4.png#averageHue=%23f4f1ed&clientId=ucf82ab94-8d3e-4&from=paste&height=796&id=udadda7ef&originHeight=876&originWidth=1170&originalType=binary&ratio=1&rotation=0&showTitle=false&size=262776&status=done&style=none&taskId=u11468233-42f1-49ea-a16a-34902b65486&title=&width=1063.636340582667)
<a name="Kqprs"></a>
# 5.总结
- simGCL中作者认为：特征分布的均匀性是影响推荐效果的潜在决定性因素，优化InfoNCE带来了更均匀的特征表示；
- DrectAu强调均匀性和对齐性都对推荐效果有较大的影响；
- 个人认为，本文最小化结构邻居对比是GCN高阶协作信号传递的另一种思路；
  最小化语义邻居在一定程度上可以看做是对特征对齐性的优化；

