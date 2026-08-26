# CVPR 2016 - ResNet Intro写作思路剖析

> 论文：Deep Residual Learning for Image Recognition 会议：CVPR 2016 原文链接：
> https://www.cv-foundation.org/openaccess/content_cvpr_2016/papers/He_Deep_Residual_Learning_CVPR_2016_paper.pdf

一般来说，Introduction 可以看作整篇论文的"压缩版"：用最少的篇幅把研究对象讲清楚，把问题为什么难讲透，再把我们的方法为什么必要讲明白。

一个清晰的写作组织是：

1. **场景与背景**：先用一个典型应用场景/运行例子引出研究背景与需求
2. **现有工作与局限**：随后对现有代表性工作进行归纳，找其在关键假设、数据特性、工作负载与系统约束下暴露的主要局限（通常不超过3点）
3. **问题刻画与目标**：在此基础上进一步刻画该问题的本质属性与硬约束（例如规模、动态性、异构性、端到端开销、正确性/一致性要求等），从而自然导出本文要解决的研究目标或问题定义（Our Goal / Problem Formulation），或支撑方案设计的核心洞见（Key Idea）
4. **关键挑战**：接着，需要明确实现该目标所面临的关键挑战（通常不超过3点），解释为何直接套用或简单扩展已有方法难以奏效
5. **方法总览**：最后给出与挑战一一对应的方法总览（整体框架与关键模块）
6. **贡献总结**：以贡献点收束，包括问题定义/设定（若有）、系统/框架设计、1-2个关键技术亮点以及充分的实验评估与分析

## Introduction 写作/构思的 Flowchart

Flowchart核心逻辑链：

> 研究背景 → 研究前沿（现有方法） → Limitations（不超过 3 点） → Key Idea / Our Goal → Challenges（不超过 3 点） → 方法总览 → 贡献点

## 快速定位：这篇论文是什么类型？

**Technique paper（新方法解决既有问题）**
- 主轴：Key Idea / Mechanism
- Goal：一句话交代即可

**Propose a New Research Problem/Setting（新问题/新设定/新任务）**
- 主轴：Our Goal / Problem Formulation（问题定义本身是贡献）
- Key Idea：作为"为什么这样定义合理/可行"的支撑

**Phenomenon Explanation(现象解释类)**
- 主轴：Summary of common problems（归纳若干方法的存在的统一共性问题）
- Key Idea：解释清楚共性问题产生的原因，给出解决方法(总结出共性问题已经是大部头的工作，只需在这基础上探索出一小步的解决方案)

**ResNet 的类型判断**：**Technique paper**（新方法解决既有问题——针对深度网络的退化问题提出残差学习框架）。主轴是 Key Idea / 机制性假设（残差映射比原始映射更易优化），Goal 仅一句话交代。

**值得注意（ResNet 作为 Technique paper 的经典之处）**：它没有罗列 3 个 limitation，而是聚焦*唯一核心局限（degradation）*，并用"构造解论证"把它从现象层面（精度退化）提升到本质层面（**优化困难**，而非表示能力不足）。Key Idea 因此与问题本质严丝合缝——残差学习直接对症优化困难。正因如此，ResNet 无需显式罗列技术挑战：非平凡性论证已内化在问题属性解读之中。

## 思考模板

| 逻辑阶段 | 原文内容与写作思路剖析 |
|---|---|
| **研究背景** | |
| 研究场景是什么？为什么重要？需要一个清晰的场景定义 + 研究动机 | "Deep convolutional neural networks have led to a series of breakthroughs for image classification. Deep networks naturally integrate low/mid/high-level features and classifiers in an end-to-end multilayer fashion, and the "levels" of features can be enriched by the number of stacked layers (depth)." 以及 "Recent evidence reveals that network depth is of crucial importance, and leading results on the challenging ImageNet dataset all exploit "very deep" models, with a depth of sixteen to thirty. Many other nontrivial visual recognition tasks have also greatly benefited from very deep models." **写作思路**：ResNet 以"深度的重要性"作为场景定义与研究动机——先用深度 CNN 的分类突破带出"端到端多层结构"这一技术语境，再强调 ImageNet 领先结果均采用 16~30 层的"极深"模型，把全文主线锁定在"深度"上。ResNet 直接从技术主线切入，场景与动机合二为一。 |
| **研究问题分析与属性解读** | |
| Limitation 1 | "Driven by the significance of depth, a question arises: Is learning better networks as easy as stacking more layers?" 承上启下提问之后，先"排除"已解决的旧障碍："An obstacle to answering this question was the notorious problem of vanishing/exploding gradients, which hamper convergence from the beginning. This problem, however, has been largely addressed by normalized initialization and intermediate normalization layers, which enable networks with tens of layers to start converging for stochastic gradient descent (SGD) with backpropagation."（**注意：梯度消失/爆炸是"已解决"的旧障碍，写作上只起铺垫作用，不是本文的 Limitation**）。随后亮出唯一核心局限为**退化问题**："When deeper networks are able to start converging, a degradation problem has been exposed: with the network depth increasing, accuracy gets saturated (which might be unsurprising) and then degrades rapidly." 并用关键否定提升问题分量："Unexpectedly, such degradation is not caused by overfitting, and adding more layers to a suitably deep model leads to higher training error, as reported in [10,41] and thoroughly verified by our experiments. Fig.1 shows a typical example." **写作思路**：由背景自然提问，把读者带到真正的问题面前。ResNet 没有罗列 3 个局限，而是聚焦退化问题这一个核心局限；"不是过拟合"的否定尤为关键——它排除了最自然的解释，迫使读者追问更深层的原因。 |
| Limitation 2 | **属性解读（立论基石）**：用构造解论证把退化问题从"精度现象"解读为"优化困难"。"The degradation (of training accuracy) indicates that not all systems are similarly easy to optimize. Let us consider a shallower architecture and its deeper counterpart that adds more layers onto it. There exists a solution by construction to the deeper model: the added layers are identity mapping, and the other layers are copied from the learned shallower model. The existence of this constructed solution indicates that a deeper model should produce no higher training error than its shallower counterpart." 由此否定"表示能力不足"的解释，并得出本质结论："But experiments show that our current solvers on hand are unable to find solutions that are comparably good or better than the constructed solution (or unable to do so in feasible time)." **写作思路**：构造解论证是 ResNet Intro 最精彩的——不满足于描述现象，而是通过"存在性论证"完成归因：深网络表达能力（解的存在性）没有问题，退化本质是**优化困难**，而非表示能力不足。这一步把 Limitation 提升为立论基石，为 Key Idea 直接铺路。 |
| Limitation 3 | 这篇论文没有讨论第三点局限。ResNet 聚焦*唯一核心局限（degradation）*，并用"构造解论证"把它从现象层面（精度退化）提升到本质层面（**优化困难**，而非表示能力不足）。Key Idea 因此与问题本质严丝合缝——残差学习直接对症优化困难。正因如此，ResNet 无需显式罗列技术挑战：非平凡性论证已内化在问题属性解读之中。 |
| **论文的 Novelty 和创新思路讨论** | |
| Key Idea | 符合"新方法解决既有问题"的判定——主轴是机制性假设，Goal 仅一句话交代："In this paper, we address the degradation problem by introducing a deep residual learning framework." 核心洞见分三步展开：① **目标重构**："Instead of hoping each few stacked layers directly fit a desired underlying mapping, we explicitly let these layers fit a residual mapping. Formally, denoting the desired underlying mapping as H(x), we let the stacked nonlinear layers fit another mapping of F(x) = H(x) − x. The original mapping is recast into F(x) + x."；② **核心假设（Hypothesis）**："We hypothesize that it is easier to optimize the residual mapping than to optimize the original, unreferenced mapping."；③ **极端情形论证**："To the extreme, if an identity mapping were optimal, it would be easier to push the residual to zero than to fit an identity mapping by a stack of nonlinear layers." **写作思路**：创新思路与局限一一对应，一个 Key Idea 直接对症唯一核心局限（优化困难）。先给出目标映射的重构，再给出"为什么更易优化"的假设，并用极端情形论证让假设直观可信。 |
| Challenges | **NA**。ResNet 没有单独讨论技术挑战，nontrivial 论证分两半完成：前一半在"属性解读"（构造解论证表明直接加深不可行——求解器找不到构造解，plain net 无效），后一半在"方法总览"（零开销、训练机制不变）。隐含挑战有二：① **优化困难**——求解器找不到构造解，直接堆层（plain net）无效；② **工程约束**——新结构需不增参数/计算复杂度、不破坏端到端 SGD+BP 与易实现性。AI 顶会论文经常采用这种写法（不讲 Challenges，直接讲 Our Methodology）。 |
| 方法总览 | "The formulation of F(x) + x can be realized by feedforward neural networks with "shortcut connections" (Fig.2)." 方法总览由三个技术点组成，与隐含挑战一一对应：① **恒等跳连**（实现残差公式）："Shortcut connections [2,33,48] are those skipping one or more layers. In our case, the shortcut connections simply perform identity mapping, and their outputs are added to the outputs of the stacked layers (Fig.2)."；② **零开销**（满足工程约束）："Identity shortcut connections add neither extra parameter nor computational complexity."；③ **可训练性与易实现性**："The entire network can still be trained end-to-end by SGD with backpropagation, and can be easily implemented using common libraries (e.g., Caffe[19]) without modifying the solvers." **写作思路**：ResNet 的方法总览很短——核心机制（残差映射）已在 Key Idea 段讲透，这里只需回答"怎么落地、落地是否便宜、是否破坏既有训练机制"三个工程问题。 |
| 贡献点 | ResNet 以"实验结论 → 泛化性 → 竞赛结果"三级递进收束，把贡献从方法本身推广到原理的普适性。① **ImageNet 综合实验（2 点结论）**："We present comprehensive experiments on ImageNet to show the degradation problem and evaluate our method. We show that: (1) Our extremely deep residual nets are easy to optimize, but the counterpart "plain" nets (that simply stack layers) exhibit higher training error when the depth increases; (2) Our deep residual nets can easily enjoy accuracy gains from greatly increased depth, producing results substantially better than previous networks."；② **泛化性（CIFAR-10）**："Similar phenomena are also shown on the CIFAR-10 set, suggesting that optimization difficulties and the effects of our method are not just akin to a particular dataset. We present successfully trained models on this dataset with over 100 layers, and explore models with over 1000 layers."；③ **结果与竞赛**："On the ImageNet classification dataset [35], we obtain excellent results by extremely deep residual nets. Our 152-layer residual net is the deepest network ever presented on ImageNet, while still having lower complexity than VGG nets [40]. Our ensemble has 3.57% top-5 error on the ImageNet test set, and won the 1st place in the ILSVRC 2015 classification competition. The extremely deep representations also have excellent generalization performance on other recognition tasks, and lead us to further win the 1st places on: ImageNet detection, ImageNet localization, COCO detection, and COCO segmentation in ILSVRC & COCO 2015 competitions. This strong evidence shows that the residual learning principle is generic, and we expect that it is applicable in other vision and non-vision problems." **写作思路**：注意贡献点与 Key Idea 首尾呼应——结论 (1) 验证"易优化"（对症 Limitation），结论 (2) 验证"精度增益"；CIFAR-10 实验排除了"数据集特例"的可能；竞赛大满贯把"方法有效"升级为"原理普适"。 |

## 📝 Writing Template

（ResNet Introduction 中的高频句式与词汇，可直接套用）

**场景与动机**
- "Deep convolutional neural networks have led to a series of breakthroughs for xxx."
- "Recent evidence reveals that xxx is of crucial importance."

**承上启下提问**
- "Driven by the significance of xxx, a question arises: Is learning better networks as easy as stacking more layers?"

**障碍铺垫与排除**
- "An obstacle to answering this question was the notorious problem of xxx... This problem, however, has been largely addressed by xxx..."

**现象描述 + 否定式归因**
- "When deeper networks are able to start converging, a degradation problem has been exposed: ..."
- "Unexpectedly, such degradation is not caused by overfitting..."

**构造解论证**（存在性论证，用于定位问题本质）
- "There exists a solution by construction to the deeper model: the added layers are identity mapping, and the other layers are copied from the learned shallower model."

**目标重构**
- "Instead of hoping each few stacked layers directly fit a desired underlying mapping, we explicitly let these layers fit a residual mapping."

**核心假设**
- "We hypothesize that it is easier to optimize the residual mapping than to optimize the original, unreferenced mapping."

**极端情形论证**
- "To the extreme, if an identity mapping were optimal, it would be easier to push the residual to zero than to fit an identity mapping by a stack of nonlinear layers."

**收束与展望**
- "This strong evidence shows that the residual learning principle is generic, and we expect that it is applicable in other vision and non-vision problems."

**高频词汇**
- degradation（退化）· saturation（饱和）· counterpart（对照网络/对应物）· by construction（构造性地）· recast（重构）· unreferenced mapping（无参照映射）· hypothesize（假设）· nontrivial（非平凡的）· generic（普适的）

## 🙏 Acknowledgment

Intro写作的思考模版 Markdown 继承自：[Supervisor-Skills](https://github.com/HKUSTDial/Supervisor-Skills)

由衷感谢 **香港科技大学（广州）骆昱宇** 老师团队的开源项目！ 💐🌹🌹
