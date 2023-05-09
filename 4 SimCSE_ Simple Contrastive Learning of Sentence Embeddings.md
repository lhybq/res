**4 SimCSE:句嵌入的简单对比学习**

[TOC]

---

# 概要
| | SimCSE |
| --- | --- |
| **题目** | SimCSE: Simple Contrastive Learning of Sentence Embeddings |
| **来源** | EMNLP 2021 |
| **关键** | 对比学习；NLP |
| **代码** | [GitHub - princeton-nlp/SimCSE: EMNLP′2021: SimCSE: Simple Contrastive Learning of Sentence Embeddings https://arxiv.org/abs/2104.08821](https://github.com/princeton-nlp/SimCSE)<br />[GitHub - bojone/SimCSE: SimCSE在中文任务上的简单实验](https://github.com/bojone/SimCSE) |
| **作者** | 普林斯顿大学计算机科学系 Tianyu Gao； Danqi Chen<br />清华大学跨学科信息科学研究所  Xingcheng Yao |
| **摘要** | 一个简单的对比学习框架SimCSE，极大提高了句子嵌入的。<br />首先描述了一种无监督方法，它采用输入句子并在对比目标中预测自己，仅使用标准 dropout 作为噪声。这种简单的方法效果出奇地好，表现与以前的监督方法相当。我们发现 dropout 充当最小的数据增强，删除它会导致表示崩溃。然后，我们提出了一种监督方法，通过使用“蕴含”对作为正例，将自然语言推理数据集中的注释对合并到我们的对比学习框架中，将“矛盾”对作为硬否定。我们在标准语义文本相似度 (STS) 任务上评估 SimCSE，我们使用 BERTbase 的无监督和监督模型的平均 Spearman 相关性分别为 76.3% 和 81.6%，与之前的最佳结果相比提高了 4.2% 和 2.2%。我们还从理论上和经验上表明，对比学习目标将预训练的嵌入的各向异性空间正则化为更均匀，当监督信号可用时，它更好地对齐正对。 |
| **评价** | <br /><br /> |
| **笔记** | [文本匹配之SimCSE模型_dzysunshine的博客-CSDN博客](https://blog.csdn.net/dzysunshine/article/details/124493089)<br />[【论文复现】SimCSE对比学习: 文本增广是什么牛马，我只需要简单Dropout两下_是算法不是法术的博客-CSDN博客](https://blog.csdn.net/weixin_45839693/article/details/116302914)<br />[对比学习SimCSE](https://www.jianshu.com/p/ebe95c24bac0) |
| **模型框架** |  |

# 1.动机
# 2.相关工作
# 3.研究方法
# 4.实验
# 5.总结
> SimCSE，直译过来就是 基于简单对比学习句子嵌入。和没有对比学习的Bert相比，最大的区别就是：
> - 考虑到了拉大当前样本和不相关样本的距离，uniformity。 而原始的Bert仅仅考虑了相似样本越相似越好，也就是Alignment。


