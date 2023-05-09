**1 GDE_Less is more，更轻量化的图卷积推荐系统**

[TOC]

---

# 概要
| | GDE |
| --- | --- |
| **题目** | Less is More: Reweighting Important Spectral Graph Features for Recommendation |
| **来源** | SIGIR 2022 |
| **关键** | CF; GCN；      频谱分析；降维思想，带通滤波器降噪；动态负采样加权； |
| **代码** | [https://github.com/tanatosuu/GDE](https://github.com/tanatosuu/GDE) |
| **作者** | Shaowen Peng(Kyoto University) |
| **摘要** | 由于图卷积网络 (GCN) 在推荐系统和协同过滤 (CF) 方面取得了巨大成功，因此它们的机制，尤其是核心组件（即邻域聚合）对推荐的贡献尚未得到很好的研究。为了揭示GCNs推荐的有效性，我们首先从光谱的角度分析它们，发现两个重要的发现:<br />(1)只有一小部分谱图特征强调邻域平滑性和差异有助于推荐的准确性，而大多数图信息可以被认为是噪声，甚至会降低性能;<br />(2)邻域聚合的重复强调平滑的特征，并以无效的方式过滤掉噪声信息。<br />基于上述两个发现，提出了一种新的GCN学习方案，通过用一种简单而有效的图去噪编码器(GDE)替换邻域聚合来进行推荐，该编码器充当带通滤波器来捕获重要的图特征。<br />我们表明，我们提出的方法减轻了过度平滑，并且与可以考虑任何跳邻域的不定层 GCN 相当。最后，我们动态调整负样本的梯度，以加快模型训练，而不会引入额外的复杂性。在五个真实数据集上的广泛实验表明，我们提出的方法不仅优于最先进的方法，而且比 LightGCN 实现了 12 倍的加速。 |
| **评价** | **频域图卷积的特征向量重要性赋权**<br />1、在频域下对图卷积进行分析；<br />2、提出了一个高效的GCN（超图卷积），作为带通滤波器；<br />3、动态调整negative sample的梯度以加快收敛 |
| **参考笔记** |  |
| **模型框架** | ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677680473614-b5117c5f-1bae-4c08-b9c5-1e9c62b81e03.png#averageHue=%23faf9f8&clientId=u56e112a2-e783-4&from=paste&height=357&id=u066f7cb0&originHeight=393&originWidth=641&originalType=binary&ratio=1&rotation=0&showTitle=false&size=46854&status=done&style=none&taskId=uaa27ea2e-be0b-43cc-8a21-049cb8302bf&title=&width=582.7272600969995) |

---

<a name="RqZ57"></a>
## 思路整理
<a name="zCNBi"></a>
#### 1、提出三个问题：

   1. 图信息中那些对推荐重要；
   2. 领域聚合是如何提高推荐准确性；
   3. 尝试更有效的方式取代/优化领域聚合；
:::
<a name="UQhlC"></a>
#### 2、从频谱的角度分析得到一下发现：


1. 分析**邻域平滑度和差异**，这显着影响推荐准确性，而只考虑光谱特征的一小部分，而大多数图信息没有积极影响，可被认为是图上添加的噪声；
2. GCN 中的**堆叠层**倾向于**强调图的平滑度并抑制其他信息**。

文章发现邻域聚合的缺陷，并提出图的去噪解码器 (GDE) 替换领域聚合，前者只保留重要的图特征来模拟图平滑度和差异，而无需堆叠层。
:::
<a name="CV4aY"></a>
#### 3、基于1与2，文章主要有以下4大工作：


1. 光谱的角度分析领域聚合的缺陷；
2. 设计GDE取代堆叠许多层来捕获高阶领域信息，并替代领域聚合；
3. 提出负样本动态调整梯度（不太理解，可以看一看）；
4. 5个数据集上进行试验；

<a name="HP1DO"></a>
## 准备工作
<a name="FMxIe"></a>
### Graph Convolutional Network for CF
<a name="HQOk8"></a>
### Graph Signal Processing
图信号处理，定义图信号量![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677132483302-50ecba64-1301-4b99-a969-39f0e55ec82a.png#averageHue=%23f7f3f0&clientId=u11f5d688-b8be-4&from=paste&height=36&id=u1df9dfed&originHeight=36&originWidth=103&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1332&status=done&style=none&taskId=u734f4a9f-f2e3-45eb-a332-d5ea30df6e9&title=&width=103)，其变化衡量每个节点的信号与其邻域之间的差异，即变化较小的信号量意味着每个节点与其邻域之间的高度平滑，而变化很大的信号强调它们之间的差异。
> ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677465292711-45d00a74-c1ee-46c4-8f30-c2fe312ea616.png#averageHue=%23f6f2ef&clientId=u18553905-8946-4&from=paste&height=70&id=ufdeeb25e&originHeight=70&originWidth=642&originalType=binary&ratio=1&rotation=0&showTitle=false&size=15611&status=done&style=none&taskId=uf64c6199-39ab-4554-9bef-15280590c2f&title=&width=642)

根据![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677502549014-2ab78e39-1fd2-4b95-9072-22c2a9ea9b65.png#averageHue=%23f6f4c2&clientId=ubb8ba1de-7d4f-4&from=paste&height=36&id=u21055148&originHeight=36&originWidth=321&originalType=binary&ratio=1&rotation=0&showTitle=false&size=6982&status=done&style=none&taskId=ub07b1065-6f97-4e0d-81de-90e9b18570a&title=&width=321)，图信息由正交谱特征组成。通过定义 1，具有较大特征值的 v𝑡 更平滑（较小的变化），而特征值较小的特征较粗糙（较大的变化）。
> - 那么这个variation捕捉的就是，一个向量，在图卷积中，作为一个邻居，卷到其他node的信息，和原始的node embedding的差距。

> - 可以看出来，如果variation小，说明此时图卷积，捕捉的是这个节点的平滑信息。如果variation大，那说明在图卷积过程中，图卷积强调它和邻居的差异性。

> - 同时，vt的variation的大小又和其特征值λt挂钩，特征值越大，variation越小特征向量就越平滑；特征值越小，variation越大特征向量就越粗糙。

> - 注意到这里关于特征值大小和信息的平滑程度的关系，和我们以前说的不一样，以前是越平滑的信息，他的特征值越小，也称之为低频信号。但是这里是特征值越大越平滑，是因为以前我们讲的是拉普拉斯矩阵L的特征值和特征向量，这里说的是邻接矩阵A的特征值和特征向量。

---

<a name="mf4DK"></a>
## 主要工作
<a name="XYYgB"></a>
### 频谱视角分析
1、（引用LightGCN的工作，提出GCN的领域聚合重要性）在定义1基础上，从光谱视角分析GCN的领域聚合效果。<br />定义有过滤作用的邻接矩阵，![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677678283248-d979ee21-d7b4-4b5d-859c-703d15cbf0f2.png#averageHue=%23f8f6f5&clientId=u56e112a2-e783-4&from=paste&height=59&id=u3c151478&originHeight=59&originWidth=237&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3149&status=done&style=none&taskId=u6c4a4230-2f3f-40cd-af44-f71c0db8ed0&title=&width=237)，其中M (𝜆𝑡 ) = {0, 𝜆𝑡 }，可以保留特征值对应图结点的不同光滑程度的信息。![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677678609787-8d45713d-faf7-4847-bae0-908a47bcf513.png#averageHue=%23f6f3f0&clientId=u56e112a2-e783-4&from=paste&height=28&id=u5d3e9bb4&originHeight=28&originWidth=39&originalType=binary&ratio=1&rotation=0&showTitle=false&size=586&status=done&style=none&taskId=ua466627e-9ebb-4f50-bffd-dbc1dbebe2d&title=&width=39)替换模型vanilla GCN [ 19 ] and LightGCN [ 14 ] 两个模型中的A，进行实验观察。![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677679495565-ab8bd29f-7650-4968-9a3c-2005d33b9cea.png#averageHue=%23f7f5f5&clientId=u56e112a2-e783-4&from=paste&height=331&id=u03b3d76c&originHeight=331&originWidth=1313&originalType=binary&ratio=1&rotation=0&showTitle=false&size=72494&status=done&style=none&taskId=u3fe0a462-5a56-4211-9793-ea88abfb952&title=&width=1313)

解释实验现象的原因：为什么准确度主要由最平滑和粗略的特征贡献。根据定义，这两种特征实际上代表了用户行为的趋势：同质性和异质性，用户倾向于与相似的其他人（即，在邻域中）和不同的其他人（即与用户无关）交互，而大多数不是相当平滑或粗糙的特征对强调这两种效果不太有帮助，因此对准确性的贡献较小。

> - 只有一小部分高度平滑和高度粗糙的信号是有助于推荐的，其他的信号都可以认定为是噪声
> - 平滑的信号的作用大于粗糙的信号。

2、分析局限性（这里主要是根据实验观察，分析lightgcn的局限性）
> ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678071785537-4b47db71-996d-4e71-a285-74a9891548c9.png#averageHue=%23f6f5f4&clientId=u6ef54873-fad6-4&from=paste&height=466&id=u991a4bb3&originHeight=513&originWidth=740&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=135383&status=done&style=none&taskId=ue5e08a2a-847d-46f8-adf0-fc2fe364efc&title=&width=672.7272581463021)

> ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678071757567-218dce8d-00e8-48da-b11a-85d654f7e212.png#averageHue=%23f7f5f3&clientId=u6ef54873-fad6-4&from=paste&height=420&id=ud9660f04&originHeight=462&originWidth=798&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=50123&status=done&style=none&taskId=u8d0a684d-94cc-423b-8cb9-02ced5d3613&title=&width=725.454529730742)

其中![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677679699428-2ea93d4b-77cb-4557-8b18-0f1142b2cbc1.png#averageHue=%23f3f2f0&clientId=u56e112a2-e783-4&from=paste&height=22&id=ud51e19e5&originHeight=28&originWidth=104&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1078&status=done&style=none&taskId=u6e83db23-a2a2-43db-b419-4cba82fe56e&title=&width=83.2)是归一化的拉普拉斯矩阵，它测量每个节点与其邻域之间的差异。很容易验证 L 的特征值为![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677679722485-7c1aee89-6050-48b7-baab-7cf00fc3f7cb.png#averageHue=%23f7f4f2&clientId=u56e112a2-e783-4&from=paste&height=23&id=ucbdee854&originHeight=29&originWidth=109&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1292&status=done&style=none&taskId=u39b9fd29-fbd6-4123-ad1c-3f51373f4f2&title=&width=87.2)，其中粗糙特征被强调和平滑的特征被抑制。因为特征值越大，![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677132483302-50ecba64-1301-4b99-a969-39f0e55ec82a.png#averageHue=%23f7f3f0&clientId=u11f5d688-b8be-4&from=paste&height=36&id=ntgb8&originHeight=36&originWidth=103&originalType=binary&ratio=1&rotation=0&showTitle=false&size=1332&status=done&style=none&taskId=u734f4a9f-f2e3-45eb-a332-d5ea30df6e9&title=&width=103)越小，即表现为越平滑，在每一轮得到的权重越小；特征值越小则相反，因此认为原有的模型没有很好地捕获影响更大的平滑信息，故文章认为有必要提出一种新的GCN方法，即GDE，适应推荐场景。

<a name="MGZD6"></a>
### 提出方法（GDE）
 ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677678283248-d979ee21-d7b4-4b5d-859c-703d15cbf0f2.png#averageHue=%23f8f6f5&clientId=u56e112a2-e783-4&from=paste&height=59&id=BH0cu&originHeight=59&originWidth=237&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3149&status=done&style=none&taskId=u6c4a4230-2f3f-40cd-af44-f71c0db8ed0&title=&width=237)，所提GDE 的一般思想为，将交互图G划分为平滑的G𝑆、粗糙G𝑅和嘈杂的G𝑁图，分别由平滑、粗糙和嘈杂的谱特征组成，最终嵌入由 G𝑆 和 G𝑅 生成的嵌入贡献，而来自 G𝑁 的嵌入被过滤掉。所以GDE 可以表述为带通滤波器：<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1677735318329-cf0f1689-ca73-443d-8b36-95674d386479.png#averageHue=%23fbfaf9&clientId=u56e112a2-e783-4&from=paste&height=100&id=u0aec3ed1&originHeight=110&originWidth=398&originalType=binary&ratio=1&rotation=0&showTitle=false&size=7539&status=done&style=none&taskId=u406eb21f-48ef-4a12-b44e-9051f4ecc72&title=&width=361.8181739759841)<br />（其中![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678071854109-cea9c6d8-cfe9-4431-9ec3-f7fd1c954c1f.png#averageHue=%23f8f5f1&clientId=u6ef54873-fad6-4&from=paste&height=37&id=u79d27181&originHeight=41&originWidth=92&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=1778&status=done&style=none&taskId=u9004f57b-f25a-4f37-8b66-d6d5447b841&title=&width=83.63636182359431))可以理解为频率响应函数，也可以理解为滤波器，也可以理解为第t个node feature重要性评估函数）
> ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678071891697-aaa36874-a430-4d2d-97fd-9d4df686d1d5.png#averageHue=%23f5f3f2&clientId=u6ef54873-fad6-4&from=paste&height=172&id=u6130768d&originHeight=189&originWidth=588&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=22908&status=done&style=none&taskId=ud12eeda2-40b9-4076-b5ab-0def6c226df&title=&width=534.545442959494)

> <a name="UKgZ0"></a>
![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678071916734-5f26f05e-8424-4151-9ddc-24385ad776bd.png#averageHue=%23f3f1f0&clientId=u6ef54873-fad6-4&from=paste&height=470&id=u3aacae6e&originHeight=517&originWidth=759&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=98197&status=done&style=none&taskId=u20d861c5-7984-4661-92d8-30d0054506e&title=&width=689.999985044653)
> ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678071950615-a0a9196f-8298-4f20-9e96-650bbf906311.png#averageHue=%23f5f3f2&clientId=u6ef54873-fad6-4&from=paste&height=509&id=u274e1819&originHeight=560&originWidth=754&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=82562&status=done&style=none&taskId=u8e53dd62-3d5d-46ff-8085-30cfddfb874&title=&width=685.4545305977186)
> ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678071975726-be6784d1-125f-4650-adf4-cfbcf6149f83.png#averageHue=%23faf9f8&clientId=u6ef54873-fad6-4&from=paste&height=403&id=ub2c0cf85&originHeight=443&originWidth=745&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=42903&status=done&style=none&taskId=uc561db22-a773-4aff-9811-1deb958e4d9&title=&width=677.2727125932365)

<a name="JnNPa"></a>
### 优化
> ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678072073052-094ae941-fd7c-4160-acb6-1d45eeebb4d7.png#averageHue=%23f6f5f4&clientId=u6ef54873-fad6-4&from=paste&height=200&id=u8a9d74ee&originHeight=220&originWidth=647&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=30362&status=done&style=none&taskId=u5894632d-d704-41b9-bae2-b878fe7f15c&title=&width=588.1818054333208)

<a name="gYm8B"></a>
## 总结
> ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678072110025-5ebad9cb-4ba7-4422-9442-03d72d4b0b33.png#averageHue=%23eeeae6&clientId=u6ef54873-fad6-4&from=paste&height=404&id=u38641a7a&originHeight=444&originWidth=779&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=82425&status=done&style=none&taskId=u042cb8e5-71b8-40dc-96fd-806d01e09bb&title=&width=708.181802832391)

<a name="NRfzd"></a>
## 引用
[2 Deeper Insights into Graph Convolutional Networks for Semi-Supervised Learning](https://www.yuque.com/liupetri/ztv0ka/verdb864wzhxrvgt)


