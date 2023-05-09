**7 XSimGCL：更简单的simGCL**

[TOC]

---

# 概要
| | XSimGCL |
| --- | --- |
| **题目** | XSimGCL：Towards Extremely Simple GraphContrastive Learning for Recommendation |
| **来源** | IEEE TRANSACTIONS ON KNOWLEDGE AND DATA ENGINEERING； TKDE 2022 |
| **关键** | 对比学习；数据增强 |
| **代码** |  |
| **作者** | ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1681370540297-6b39e02e-77a3-4521-976f-7f0d91f25c04.png#averageHue=%23f0edea&clientId=u5677e761-846d-4&from=paste&height=42&id=u6801d07d&originHeight=53&originWidth=1056&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=9871&status=done&style=none&taskId=ue206c94f-58ce-47d3-b6e8-49cf425bfb9&title=&width=844.8) |
| **摘要** | 对比学习（CL）最近被证明在提高推荐性能方面至关重要。基于CL的推荐模型的基本思想是最大限度地提高从用户项二分图的不同图扩充中学习到的表示之间的一致性。以这种自我监督的方式，基于CL的推荐模型有望从原始数据中提取一般特征，以解决数据稀疏性问题。尽管这种模式很有效，但我们仍然不知道业绩增长的基础是什么。</br>在本文中，我们首先揭示了CL通过赋予模型学习更均匀分布的用户/项目表示的能力来增强推荐，这可以隐含地缓解普遍存在的流行偏见，并推广长尾项目。同时，我们发现，在先前的研究中被认为是必要的图增强，在基于CL的推荐中相对不可靠且不那么重要。在这些发现的基础上，我们提出了一种极其简单的图对比学习方法（XSimGCL）进行推荐，该方法摒弃了无效的图增强，而是采用简单而有效的基于噪声的嵌入增强来创建CL的视图。</br>一项对三个大型且高度稀疏的基准数据集的综合实验研究表明，尽管所提出的方法非常简单，但它可以平滑地调整学习表示的一致性，并且在推荐精度和训练效率方面都大大优于基于图扩充的方法。 |
| **评价** | ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1681370609168-232cf3e3-fec2-4543-93f4-70b68e7b6e7d.png#averageHue=%23f2f1f0&clientId=u5677e761-846d-4&from=paste&height=382&id=uc884b683&originHeight=477&originWidth=1212&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=189782&status=done&style=none&taskId=u9cbfa0f3-6080-476c-86c1-3fb9d09ab14&title=&width=969.6) |
| **笔记** | [XSimGCL: Towards Extremely Simple GraphContrastive Learning for Recommendation 论文+代码解读_只想做个咸鱼的博客-CSDN博客](https://blog.csdn.net/zhao254014/article/details/127000889) |
| **模型框架** | ![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1681370439153-8a41b927-7936-411e-a4e3-b1e9bd9834e0.png#averageHue=%23f4f3f0&clientId=u5677e761-846d-4&from=paste&height=591&id=u991589d8&originHeight=739&originWidth=593&originalType=binary&ratio=1.25&rotation=0&showTitle=false&size=67671&status=done&style=none&taskId=ud753cc32-83ec-40ee-b3a0-676581aff50&title=&width=474.4) || 
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

