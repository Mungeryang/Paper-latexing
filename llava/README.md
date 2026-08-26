# NeurIPS'23 Oral  - LLaVA Intro写作思路剖析

> 论文：Visual Instruction Tuning 会议：NeurIPS 2023 原文链接：
> [https://proceedings.neurips.cc/paper_files/paper/2023/file/6dcf277ea32ce3288914faf369fe6de0-Paper-Conference.pdf](https://proceedings.neurips.cc/paper_files/paper/2023/file/6dcf277ea32ce3288914faf369fe6de0-Paper-Conference.pdf)

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

**LLaVA 的类型判断**：最接近 **Technique paper**（新方法解决既有问题——把 NLP 中验证有效的指令微调范式迁移到多模态），但"新设定"的成分也很重：视觉指令微调本身 + GPT-4 数据生成管线 + 两个 LLaVA-Bench 都属于新资源/新设定层面的贡献。

## 思考模板


| 逻辑阶段                                 | 原文内容与写作思路剖析                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **研究背景**                             |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| 研究场景是什么？为什么重要？需要一个清晰的场景定义 + 研究动机     | "Humans interact with the world through many channels such as vision and language, as each individual channel has a unique advantage in representing and communicating certain concepts, and thus facilitates a better understanding of the world. One of the core aspirations in artificial intelligence is to develop a general-purpose assistant that can effectively follow multi-model vision-and-language instructions, aligned with human intent to complete various real-world tasks in the wild."**写作思路**：通过人类与世界多感官通道交互，阐述多模态信息的重要性；进而引出当前AI领域的核心目标是研究多模态“视觉-语言”Assistant，以便完成各种真实任务。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| **研究问题分析与属性解读**                      |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Limitation 1                         | "In this line of work, each task is solved independently by one single large vision model, with the task instruction implicitly considered in the model design. Further, language is only utilized to describe the image content. While this allows language to play an important role in mapping visual signals to language semantics, it leads to models that usually have a fixed interface with interactivity and adaptability to the user's instruction." **写作思路**：分析"语言增强的视觉基础模型"这条技术路线的不足：每个任务由单个视觉模型独立解决，指令隐含在模型设计中；语言只被用来描述图像内容，导致模型接口固定，难以适应用户指令。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Limitation 2                         | "For example, the recent success of ChatGPT and GPT-4 have demonstrated the power of aligned LLMs in following human instructions, and have stimulated tremendous interest in developing open-source LLMs.Among them, LLaMA is an open source LLM that matches the performance of GPT-3. Alpaca, Vicuna, GPT-4-LLM utilize various machine-generated high quality instruction-following samples to improve the LLM's alignment ability, reporting impressive performance compared with proprietary LLMs. Importantly, this line of work is *text-only*." **写作思路**：分析"LLM 指令微调"这条技术路线的不足：ChatGPT/GPT-4、Alpaca、Vicuna 等虽证明了指令微调能大幅提升指令跟随能力，但这套范式是纯文本的（text-only），无法处理视觉输入。写作结构上：分别讨论两条技术路线各自的不足，再在两条路线的交叉点（多模态指令微调）上做创新。                                                                                                                                                                                                                                                                                                                                                                                                               |
| Limitation 3                         | 这篇论文没有讨论第三点局限。两条路线的局限恰好互补：视觉模型缺"通用指令接口"，文本指令微调缺"视觉通道"；二者的共同盲区正是本文的切入点。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
| **论文的 Novelty 和创新思路讨论**              |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| Key Idea                             | LLaVA 的核心洞见分两层： (1) **范式层面**：把 NLP 中被验证有效的指令微调范式迁移到"视觉-语言"多模态空间（first attempt）； (2) **执行层面**：纯文本 GPT-4 无法直接看图像，但通过符号化视觉表示（captions + bboxes）可将图像编码为 LLM 可识别序列，使语言模型充当多模态指令数据的生成器，从而解决"视觉-语言指令跟随数据缺乏"这一关键挑战。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| Challenges                           | **NA**。论文没有单独讨论技术挑战。唯一显式提出的挑战在贡献点 (1) 中："One key challenge is the lack of vision-language instruction-following data"                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| 方法总览                                 | In this paper, we present visual instruction tuning, the first attempt to extend instruction-tuning to the language-image multimodal space, to pave the way towards building a gnernal-purpose cisual assistant. **写作思路**：本文没有在Intro中大篇幅的阐述 LLaVA 的核心方法架构, 而是用一句话把核心工作解释清楚。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| 贡献点 - 本文清晰的列出了four main contribution | (1) **Multimodal instruction-following data.** One key challenge is the lack of vision-language instruction-following data. We present a data reformation perspective and pipeline to convert image-text pairs into an appropriate instruction-following format, using ChatGPT/GPT-4. (2) **Large multimodal models.** We develop a large multimodal model (LMM), by connecting the open-set visual encoder of CLIP with the language decoder Vicuna, and fine-tuning end-to-end on our generated instructional vision-language data. Our empirical study validates the effectiveness of using generated-purpose instruction-following visual agent. When ensembled with GPT-4, our approach achieves SOTA on the Science QA multimodal reasoning dataset. (3) **Multimodal instruction-following benchmark.** We present LLaVA-Bench with two challenging benchmarks, with a diverse selection of paired images, instructions and detailed annotations. (4) **Opne-source.** We release the following assets to the public: the generated multimodal instruction data, the codebase, the model checkpoints, and a visual chat demo. |




## 📝 Writing Template

On of the core aspirations in xxx is to ...

To this end, the community has witnessed an emergent intrest in developing ..., with strong capabilities on xxx .

Large Language Models (LLMs) has shown that language/vision can play a wider role in/: xxx .

For example, the recent success of xxx have demonstrated xxx , and have stimultated tremendous intrest in developing xxx .

aspirations

utilize

tremendous

method -> perspective (方法 -> 思路)

counterparts

## 🙏 Acknowledgment

Intro写作的思考模版 Markdown 继承自：[Supervisor-Skills](https://github.com/HKUSTDial/Supervisor-Skills)

由衷感谢 **香港科技大学（广州）骆昱宇** 老师团队的开源项目！ 💐🌹🌹