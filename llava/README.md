# NeurIPS'23 Oral  - LLaVA Intro写作思路剖析

> 论文：Visual Instruction Tuning 会议：NeurIPS 2023 原文链接：
> https://proceedings.neurips.cc/paper_files/paper/2023/file/6dcf277ea32ce3288914faf369fe6de0-Paper-Conference.pdf

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




## 📝 Writing Template
On of the core aspirations in xxx is to ...

To this end, the community has witnessed an emergent intrest in developing ..., with strong capabilities on xxx .

Large Language Models (LLMs) has shown that language/vision can play a wider role in/: xxx .


