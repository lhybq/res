**3 CCFCRec：基于对比协同过滤的冷启动物品推荐**

[TOC]

---

# 概要
| | CCFCRec |
| --- | --- |
| **题目** | Contrastive Collaborative Filtering for Cold-Start Item Recommendation |
| **来源** | **WWW 2023** |
| **代码** | [GitHub - zzhin/CCFCRec](https://github.com/zzhin/CCFCRec) |
| **关键** | 冷启动；对比学习； |
| **作者** | 通讯作者 四川大学 杨宁教授 |
| **摘要** | 作为一种有前景的解决方案，**基于内容的生成**模型通常将冷启动物品的内容投影到常规项目嵌入上，从物品内捕捉协作信号，从而应用协同过滤推荐。<br />然而，由于冷启动推荐模型的训练是在常规数据集上进行的，**现有的方法面临着物品协作嵌入特征模糊的问题**（即现有方法是在训练阶段模拟冷启动，从模型输入中删除常规物品的交互记录，只保留物品内容特征），这大大降低了冷启动物品推荐的性能。<br />为了解决这个问题，本文提出一种新模型，**对比协同过滤的冷启动项目推荐**(CCFCRec)，**该模型利用训练数据中的共现协同信号(the co-occurrence collaborative signals)来缓解冷启动项目推荐的嵌入协同信息模糊的问题**。即设计了一个对比协同过滤 (CF) 框架，该框架由内容 **CF 模块**和共现 **CF 模块**组成，分别生成基于内容的协作嵌入和训练项目的共现协作嵌入。<br />在两个 CF 模块的联合训练过程中，我们在两个协作嵌入之间应用了对比学习，通过对共现信号的知识可以间接转移到内容 CF 模块中，从而可以在应用阶段由记忆的共现协作信号隐式纠正模糊协作嵌入。与声音理论分析一起，在真实数据集上进行的大量实验证明了所提出模型的优越性。 |
| **评价** |  个人理解：在协同过滤推荐训练模型时，为了模拟冷启动问题（一般是基于内容的冷启动解决方法），大多数处理方法是将常规训练数据随机删除交互记录，只保留物品内容特征，但是这种模拟方法会导致协同嵌入模糊的问题，从而使得训练的推荐模型性能下降。针对现有的**基于映射**的冷启动解决方法存在的**模糊协同嵌入**的问题，提出了一种基于**对比协同过滤**的冷启动推荐算法。|
| **笔记** | [WWW2023 &#124; 对比协同过滤冷启动推荐算法](https://mp.weixin.qq.com/s/ADMMjMzLdHXVuQAwfuRHNQ)|
| **模型框架** | ![bd6a17635348cbd7d222ddf14e4fb681_2_Figure_2_-1060500954.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678283803910-46bdee5b-384c-4e4e-956b-75087a07ecb3.png#averageHue=%23fbfaf9&clientId=u5b98f1f0-e1c0-4&from=ui&id=u07941af5&originHeight=381&originWidth=594&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=22755&status=done&style=none&taskId=ubc1428a8-c6ce-4ffc-a177-f8b5929ac57&title=) |
|  |  |


<a name="swzQ4"></a>
# 1.动机
基于内容的冷启动解决方法，一般采用常规数据去除交互信息而保留内容信息的数据进行模型训练，然而会导致一个协同嵌入模糊的问题，从而导致推荐性能下降。<br />本文举例描述了基于内容的冷启动解决方案（即将冷启动物品的内容特征映射到常规物品的嵌入上，以捕捉物品内容的协同信号）会带来的问题：<br />![image.png](https://cdn.nlark.com/yuque/0/2023/png/29360045/1678284834972-50967634-b1f2-44da-90d4-52c5eef8caab.png#averageHue=%23878584&clientId=u5b98f1f0-e1c0-4&from=paste&height=853&id=uab7eafce&originHeight=853&originWidth=955&originalType=binary&ratio=1.100000023841858&rotation=0&showTitle=false&size=214217&status=done&style=none&taskId=ucb7f9c03-b7f0-46c9-b271-02630bbc552&title=&width=955)<br />因此本文提出的模型主要思想是教CF模块在**训练阶段记忆共现协同信号**，以及如何在应用模型时根据记忆的共现协同信号纠正冷启动项目的模糊CBCEs。



