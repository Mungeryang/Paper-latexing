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


## 思考模板

### 论文类型定位：Technique paper（新方法解决既有问题）


**值得注意（ResNet 作为 Technique paper 的经典之处）**：它没有罗列 3 个 limitation，而是聚焦*唯一核心局限（degradation）*，并用"构造解论证"把它从现象层面（精度退化）提升到本质层面（**优化困难**，而非表示能力不足）。Key Idea 因此与问题本质严丝合缝——残差学习直接对症优化困难。正因如此，ResNet 无需显式罗列技术挑战：非平凡性论证已内化在问题属性解读之中。

### 基于 Flowchart，转成 Table，将原文思路映射到对应的逻辑阶段

下表将 ResNet Introduction 的原文要点按 Flowchart 逻辑阶段归纳为中文提纲，可用于对照原文逐句拆分整理。

<table>
  <colgroup>
    <col style="width: 18%;">
    <col style="width: 32%;">
    <col style="width: 50%;">
  </colgroup>
  <thead>
    <tr>
      <th>逻辑阶段</th>
      <th>说明（在 ResNet 中承担的论证任务）</th>
      <th>原文内容与写作思路剖析</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>研究背景</strong></td>
      <td>研究场景是什么？为什么重要？ResNet 以"深度的重要性"作为场景定义与研究动机，把全文主线锁定在"深度"上。</td>
      <td>
        • 场景：深度 CNN 带来图像分类的系列突破，端到端多层结构自然整合低/中/高层特征与分类器
        <blockquote>Deep convolutional neural networks have led to a series of breakthroughs for image classification.<br>
        Deep networks naturally integrate low/mid/high-level features and classifiers in an end-to-end multilayer fashion, and the "levels" of features can be enriched by the number of stacked layers (depth).</blockquote>
        • 动机：网络深度至关重要——ImageNet 领先结果均采用 16~30 层的"极深"模型，许多视觉任务同样受益
        <blockquote>Recent evidence reveals that network depth is of crucial importance, and leading results on the challenging ImageNet dataset all exploit "very deep" models, with a depth of sixteen to thirty.<br>
        Many other nontrivial visual recognition tasks have also greatly benefited from very deep models.</blockquote>
      </td>
    </tr>
    <tr>
      <td rowspan="3"><strong>问题分析与属性解读</strong></td>
      <td><strong>现状分析（总述）</strong>：由背景自然提问，再"排除"已解决的旧障碍，把读者带到真正的问题面前。</td>
      <td>
        • 承上启下提问：学习更好的网络是否就像堆叠更多层一样简单？<br>
        <blockquote>Driven by the significance of depth, a question arises: Is learning better networks as easy as stacking more layers?</blockquote>
        • 障碍铺垫：梯度消失/爆炸问题已被归一化初始化 + 中间归一化层（BN）基本解决，数十层网络可经 SGD+BP 收敛（<strong>注意：这是"已解决"的旧障碍，写作上只起铺垫作用，不是本文的 Limitation</strong>）
        <blockquote>An obstacle to answering this question was the notorious problem of vanishing/exploding gradients, which hamper convergence from the beginning.<br>This problem, however, has been largely addressed by normalized initialization and intermediate normalization layers, which enable networks with tens of layers to start converging for stochastic gradient descent(SGD) with backpropagation.</blockquote>
      </td>
    </tr>
    <tr>
      <td><strong>Limitation 1（唯一核心局限）</strong>：ResNet 没有罗列 3 个局限，而是聚焦退化问题这一个核心局限，并在下一行把它从现象提升到本质。</td>
      <td>
        • 退化问题（degradation）：深度增加 → 准确率饱和（可预期）→ 迅速退化（意外）<br>
        <blockquote>When deeper networks are able to start converging, a degradation problem has been exposed: with the network depth increasing, accuracy gets saturated (which might be unsurprising) and then degrades rapidly.</blockquote>
        • 关键否定：<strong>不是过拟合</strong>——给适度深的模型再加层，<strong>训练误差</strong>反而升高（[10,41] 报告 + 本文 Fig.1 验证）
        <blockquote>Unexpectedly, such degradation is not caused by overfitting, and addingmore layers to a suitably deep models leads to higher training error, as reported in [10,41] and thoroughly verified by our experiments, Fig.1 shows a typical example.</blockquote>
      </td>
    </tr>
    <tr>
      <td><strong>属性解读（立论基石）</strong>：用构造解论证把退化问题从"精度现象"解读为"优化困难"，为 Key Idea 直接铺路。</td>
      <td>
        • 构造解论证：把浅网络已学权重复制到深层，新增层设为恒等映射 → 存在"训练误差不高于浅网络"的构造解 → 深网络表达能力（解的存在性）没有问题<br>
        <blockquote>The degradation(of training accuracy) indicates that not all systems are similarly easy to optimize. Let us consider a shallower architecture and its deeper counterpart that adds more layers onto it. There exists a solution by construction to the deeper model: the added layers are identity mapping, and the other layers are copied from the learned shallower model. The existence of this constructed solution indicates that a deeper model should produce no higher training error than its shallower counterpart.</blockquote>
        • 结论：退化本质是<strong>优化困难</strong>——现有求解器找不到与构造解相当或更好的解
        <blockquote>But experiments show that our current solvers on hand unable to find solutions that are comparably good or better than the constructed solution (or unable to do so in feasible time).</blockquote>
      </td>
    </tr>
    <tr>
      <td rowspan="2"><strong>论文的 Novelty 和创新思路讨论</strong></td>
      <td>创新思路与局限性的对应关系：一个 Key Idea 直接对症唯一核心局限（优化困难）——先给出目标映射的重构，再给出"为什么更易优化"的假设与极端情形论证。</td>
      <td>
        • 目标重构：不让堆叠层直接拟合目标底层映射 H(x)，改为显式拟合<strong>残差映射</strong> F(x) = H(x) − x，原始映射重构为 F(x) + x
        <blockquote>Instead of hoping each few stacked layers directly fit a desired underlying mapping, we explicitly let these layers fit a residual mapping. Formally, denoting the desired underlying mapping as H(x), we let the stacked nonlinear layers fit another mapping of F(x) = H(x) - x. The original mapping is recast into F(x) + x.</blockquote>
      </td>
    </tr>
    <tr>
      <td><strong>Key Idea（主轴）</strong>：符合"新方法解决既有问题"的判定——主轴是机制性假设，Goal 仅一句话交代（"address the degradation problem by introducing a deep residual learning framework"）。</td>
      <td>
        • Goal 一句话（Technique paper 的目标只需一句交代）：提出深度残差学习框架以解决退化问题
        <blockquote>In this paper, we address the degradation problem by introducing a deep residual learning framework.</blockquote>
        • 核心假设（Hypothesis）：残差映射比原始、无参照的映射<strong>更易优化</strong>
        <blockquote>We hypothesize that it is easier to optimize the residual mapping than to optimize the original, unreferenced mapping.</blockquote>
        • 极端情形论证：若恒等映射即最优，把残差推向零比用非线性层堆叠拟合恒等映射更容易
        <blockquote>To the extreme, if an identity mapping were optimal, it would be easier to push the residual to zero than to fit an identity mapping by a stack of nonlinear layers.</blockquote>
      </td>
    </tr>
    <tr>
      <td><strong>创新思路是否 Naive？<br>（技术挑战的内化）</strong></td>
      <td>ResNet 未罗列"3 个挑战"，非平凡性论证分两半完成：前一半在"属性解读"（构造解论证表明直接加深不可行），后一半在"方法总览"（实现零开销、训练机制不变）。对应模板中"投稿 AI 的会议经常不讨论技术挑战，而是直接讲 Our Methodology"的经典写法。</td>
      <td>
        • 隐含挑战 1：优化困难——求解器找不到构造解，直接堆层（plain net）无效（原文第 4 段已论证）<br>
        <blockquote></blockquote>
        • 隐含挑战 2：工程约束——新结构需不增参数/计算复杂度、不破坏端到端 SGD+BP 与易实现性<br>
        <blockquote></blockquote>
        • 非平凡性落点：Key Idea 由构造解论证导出、并非拍脑袋；恒等跳连恰好零开销满足工程约束
        <blockquote></blockquote>
      </td>
    </tr>
    <tr>
      <td rowspan="4"><strong>Our Methodology<br>（方法总览）</strong></td>
      <td>总领句：残差公式 F(x) + x 的落地方案。</td>
      <td>
        • F(x) + x 可由带 "shortcut connections" 的前馈网络实现
        <blockquote>The formulation of F(x) + x can be realized by feedforward neural networks with "shortcut connections"(Fig.2).</blockquote>
      </td>
    </tr>
    <tr>
      <td>第一个技术点：恒等跳连的结构。</td>
      <td>
        • 恒等 shortcut connections —— 跳过一个或多个层，输出与堆叠层输出直接相加
        <blockquote>Shortcut connections [2,33,48] are those skipping one or more layers. In our case, the shortcut connections simply perform identity mapping, and their outputs are added to the outputs of the stacked layers (Fig.2).</blockquote>
      </td>
    </tr>
    <tr>
      <td>第二个技术点：零开销。</td>
      <td>
        • 恒等跳连<strong>不增加任何参数，也不增加计算复杂度</strong>（原文第 6 段）
        <blockquote>Identity shortcut connections add neither extra parameter nor computational complexity.</blockquote>
      </td>
    </tr>
    <tr>
      <td>第三个技术点：可训练性与易实现性。</td>
      <td>
        • 整网仍可端到端 SGD+BP 训练；用常用库（如 Caffe[19]）即可实现，<strong>无需修改求解器</strong>
        <blockquote>The entire network can still be trained end-to-end by SGD with backpropagation, and can be easily implemented using common libraries (e.g., Caffe[19]) without modifying the solvers.</blockquote>
      </td>
    </tr>
    <tr>
      <td><strong>总结贡献点</strong></td>
      <td>ResNet 以"实验结论 → 泛化性 → 竞赛结果"三级递进收束，把贡献从方法本身推广到原理的普适性。</td>
      <td>
        • ImageNet 综合实验（2 点结论）：① 极深残差网<strong>易优化</strong>，plain 网加深训练误差升高；② 残差网从大深度获得<strong>精度增益</strong>，显著优于先前网络<br>
        <blockquote>We present comprehensive experiments on ImageNet to show the degradation problem and evaluate our method. We show that: (1)Our extremely deep residual nets are easy to optimize, but the counterpart "plain" nets (that simply stack layers) exhibit higher training error when the depth increases; (2)Our depth residual nets can easily enjoy accuracy gains from greatly increased depth, producing results substantially better than previous networks.</blockquote>
        • 泛化性：CIFAR-10 上同样现象 → 优化困难与方法效果<strong>不是数据集特例</strong>；成功训练 100+ 层，并探索 1000+ 层<br>
        <blockquote>Similar phenomena are also shown on the CIFAR-10 set, suggesting that optimization diffculties and the effects of our method are not just akin a particular dataset. We present successfully trained models on this dataset with over 100 layers, and explore models with over 1000 layers.</blockquote>
        • 结果与竞赛：152 层 = ImageNet 史上最深网络、复杂度仍低于 VGG；集成 top-5 error <strong>3.57%</strong>；获 ILSVRC 2015 分类冠军 + ImageNet 检测/定位、COCO 检测/分割冠军 → 残差学习原理<strong>通用</strong>，预期适用于其他视觉与非视觉问题
        <blockquote>On the ImageNet classification dataset [35], we obtain excellent results by extremely deep residual nets. Our 152 layers residual net is the deepest network ever presented on ImageNet, while still having lower complexity than VGG nets [40]. Our ensemble has 3.57% top-5 error on the ImageNet test set, and won the 1st place in the ILSVRC 2015 classification competition. The extremely deep representations alse have excellent generalization performance on other recognition tasks, and lead us to further win the 1st places on: ImageNet detection, ImageNet localization, COCO detection, and COCO segmentation in ILSVRC & COCO 2015 competitions. This strong evidence shows that the residual learning principle is generic, and we expect that it is applicable in other vision and non-vision problems.</blockquote>
      </td>
    </tr>
  </tbody>
</table>



## 🙏 Acknowledgment

Intro写作的思考模版 Markdown 继承自：[Supervisor-Skills](https://github.com/HKUSTDial/Supervisor-Skills)

由衷感谢 **香港科技大学（广州）骆昱宇** 老师团队的开源项目！ 💐🌹🌹




