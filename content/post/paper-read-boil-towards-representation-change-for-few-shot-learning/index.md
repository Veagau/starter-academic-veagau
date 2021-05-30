---
title: "Paper Read | BOIL: Towards Representation Change for Few-shot Learning"
date: 2021-05-30T11:10:31.266Z
draft: false
featured: false
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
## Meta Data

* Conference Name: [International Conference on Learning Representations](international-conference-on-learning-representations)
* Date: [2020/09/28](2020/09/28)
* Authors: [[Jaehoon Oh]], [[Hyungjun Yoo]], [[ChangHwan Kim]], [[Se-Young Yun]]

## Abstract

Model Agnostic Meta-Learning (MAML) is one of the most representative of gradient-based meta-learning algorithms. MAML learns new tasks with a few data samples using inner updates from a...

## Links

* [Local library](zotero://select/items/1_WNCPWRAW)
* PDF Attachments
	- [Oh et al_2020_BOIL.pdf](zotero://open-pdf/library/items/8WCW4EHK)

---
## Summary
> 写完笔记之后最后填，概述文章的内容，以后查阅笔记的时候先看这一段。注：写文章summary切记需要通过自己的思考，用自己的语言描述。忌讳直接Ctrl + c原文。

- 元学习模式中Base Learner 与Meta Learner执行的职能不同，因此在更新它们的模型参数时有必要考虑他们的更新策略是否应该保持一致。

---
## Objective(s)

> 作者的研究目标是什么？

- 本文的研究目标是：探究**表示变化**对于少样本学习终极目标（**Ultimate Goal-Solving domain-agnostic tasks**）的必要性。

---
## Background

> 研究的背景以及问题陈述：作者需要解决的问题是什么？

- 目前的假设是：影响MAML型元初始化模型表现的决定性因素是**表示复用（Representation Reuse）**而非**表示变化（Representation Change）**[^4]。
    - Representation Change假设将MAML的能力归因于躯干的更新，而Representation Reuse假设则认为网络躯干在进行Inner Loop前已经处于一种足以泛用于不同任务的状态了，因此将MAML的成功归因于Representation Reuse。
- Intriguing Question：**Is Representation reuse sufficient for meta-learning?**
- 基于Representation Reuse实现的MAML算法由于会严重依赖 **Source Domain** 和 **Target Domain** 之间的**Similarity**， 因此在Cross-domain Adaptation情况，模型表现会不尽如人意。

---
## Method(s)

> 作者解决问题的方法/算法是什么？是否基于前人的方法？基于了哪些？

- Difference between different methods

    ![image-20210530173525206](https://v-picgo-1252406892.cos.ap-chengdu.myqcloud.com/Typora/image-20210530173525206.png)

    - Head 影响决策平面的形成，而 Body 影响特征空间的分布

- Baseline

    - 基于MAML算法的改进，提出了**BOIL（Body Only update in Inner Loop）**算法——在执行**内循环参数更新**时，仅对模型的躯干（Body, Feature Extractor）进行更新，而对模型的头部（Head, Classifier）实行冻结操作。
    - 站在ANIL方法的对立面，进行对比。

- Algorithm

    - 核心操作：在元训练阶段和元测试阶段将inner update过程中的头部的更新学习率设定为0，阻止其参数更新。
        $$
        \theta _ { b , \tau _ { i } } = \theta _ { b } - \alpha _ { b } \nabla _ { \theta _ { b } } L _ { S _ { \tau _ { i } } } \left( f _ { \theta } \right) \& \theta _ { h , \tau _ { i } } = \theta _ { h } - \alpha _ { h } \nabla _ { \theta _ { h } } L _ { S _ { \tau _ { i } } } \left( f _ { \theta } \right)
        $$

        - MAML: $\alpha=\alpha_b=\alpha_h$
        - ANIL: $\alpha_b=0 \quad and \quad \alpha_h\neq 0$
        - BOIL: $\alpha_b\neq0 \quad and \quad \alpha_h = 0$

- Relation with Preconditioning Gradients[^5]

    - Preconditioning Gradient：当某一具体的层被所有任务共享时就会出现，表现为对整个空间的warp（rotating and scaling）。BOIL中冻结的Head 就可以理解为整个Body的一个Warp Layer。它的出现可以避免大容量网络的过拟合。
    - Head 在过拟合问题中非常重要，BOIL可以通过忽略Inner Loop中Head的梯度更新而成功避免过拟合问题。

---
## Evaluation

> 作者如何评估自己的方法？实验的setup是什么样的？感兴趣实验数据和结果有哪些？有没有问题或者可以借鉴的地方？

- 通过使用 Cosine Similarity、CKA和empirical results验证提出的观点。

- SetUp

    - Backbone: Conv-4 [^1]-64 channels for 84x84 & 32 channels for 32x32 | ResNet-18 [^2]

    - BatchNormalization: 在元测试阶段使用 Batch Statistics 而不是 Running Statistics.

    - Epoch: 30,000 for Conv-4 | 10,000 for ResNet-18

    - Tasks: 5 x 1,000 Tasks

    - DataSet: miniImageNet | tieredImageNet | Cars [^3] | CUB

        ![image-20210530165752166](https://v-picgo-1252406892.cos.ap-chengdu.myqcloud.com/Typora/image-20210530165752166.png)

- Experiments

    - Benchmark

        ![image-20210530174109084](https://v-picgo-1252406892.cos.ap-chengdu.myqcloud.com/Typora/image-20210530174109084.png)

        - Representation Change 在细粒度数据集（Source Domain与Target Domain之间有非常明显的相似性）

    - Cross Domain Adaptation

        ![image-20210530172249721](https://v-picgo-1252406892.cos.ap-chengdu.myqcloud.com/Typora/image-20210530172249721.png)

        - Specific to General 缺失一组CUB的实验。
        - Specific to Specific 组的实验显示Representation Change 可以使模型适应与Source Domain 完全不同的Target Domain数据。

    - Ablation Study

        ![image-20210530174746198](https://v-picgo-1252406892.cos.ap-chengdu.myqcloud.com/Typora/image-20210530174746198.png)

        - 冻结Inner Loop中的Head的参数更新效果显著。

---
## Conclusion

> 作者给出了哪些结论？哪些是strong conclusions, 哪些又是weak的conclusions（即作者并没有通过实验提供evidence，只在discussion中提到；或实验的数据并没有给出充分的evidence）?

- 特征表示向量必须快速的移动到对应的冻结分类权重向量。`？`
-  **表示变化** 在基于梯度的元学习方法中是一个关键的部件。
-  本文提出的BOIL算法：在Body的低阶部分更倾向于Representation Reuse，在Body的高阶部分更倾向于Representation Change.

---
## Notes

> (optional) 不在以上列表中，但需要特别记录的笔记。

- Writing and Spelling
    - Metric-based meta-learning (Koch, 2015; Vinyals et al., 2016; Snell et al., 2017; Sung et al., 2018) compares the distance between feature embeddings using models as a mapping function of data into an embedding space, **whereas** gradient-based meta-learning (Ravi & Larochelle, 2016; Finn et al., 2017; Nichol et al., 2018) quickly learns the parameters to be optimized when the models encounter new tasks.
    - **This algorithm has been highly influential in the field of meta-learning**, and numerous follow-up studies have been conducted (Oreshkin et al., 2018; Rusu et al., 2018; Zintgraf et al., 2018; Yoon et al., 2018; Finn et al., 2018; Triantafillou et al., 2019; Sun et al., 2019; Na et al., 2019; Tseng et al., 2020).
    - ... **attributes** the capability of MAML **to** the updates on the body
- Tricks
    - 提出了一种disconnection trick，用来移除网络中最后一个Skip Connection的Backpropagation path，这种trick可以强化躯干高阶部分的Representation Laye Change.

---
## Connected

> (optional) 列出相关性高的文献，以便之后可以继续track下去。

[^1]: Oriol Vinyals, Charles Blundell, Timothy Lillicrap, Daan Wierstra, et al. Matching networks for one shot learning. In Advances in neural information processing systems, pp. 3630–3638, 2016.
[^2]:Boris Oreshkin, Pau Rodríguez López, and Alexandre Lacoste. Tadam: Task dependent adaptive metric for improved few-shot learning. In Advances in Neural Information Processing Systems, pp. 721–731, 2018.
[^3]:Jonathan Krause, Michael Stark, Jia Deng, and Li Fei-Fei. 3d object representations for fine-grained categorization. In Proceedings of the IEEE international conference on computer vision workshops, pp. 554–561, 2013.
[^4]:Aniruddh Raghu, Maithra Raghu, Samy Bengio, and Oriol Vinyals. Rapid learning or feature reuse? towards understanding the effectiveness of maml. In International Conference on Learning Representations, 2020. URL https://openreview.net/forum?id=rkgMkCEtPB.
[^5]: Sebastian Flennerhag, Andrei A. Rusu, Razvan Pascanu, Francesco Visin, Hujun Yin, and Raia Hadsell. Meta-learning with warped gradient descent. In 8th International Conference on Learning Representations, ICLR 2020, Addis Ababa, Ethiopia, April 26-30, 2020. OpenReview.net, 2020. URL https://openreview.net/forum?id=rkeiQlBFPB.