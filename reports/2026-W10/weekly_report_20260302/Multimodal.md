# Multimodal · 2026-03-02 ~ 2026-03-06

**论文数**: 26 篇

---

## 📊 趋势分析

### 研究全貌
多模态领域的研究呈现出“深化理解”与“优化应用”并行的鲜明趋势。主要研究方向可归纳为以下几个方面：**模型评估与基准构建**、**模型架构与训练优化**、**安全与鲁棒性**以及**特定应用领域的创新**。其中，模型架构与训练优化方向尤为活跃，涌现出大量旨在提升模型**细粒度理解能力**（如3D空间推理、区域级视觉理解）和**跨模态对齐效率**的工作。安全与鲁棒性研究则聚焦于量化模型不确定性、防御多模态攻击，以支持可靠部署。当前的热点问题是如何让大模型从“看全貌”转向“看细节、看关系、看三维”，并在此过程中解决计算效率、泛化性和在复杂场景下的实用化挑战。整体研究趋势正从追求通用能力，转向关注模型的**可解释性、可控性**以及**在安全攸关场景下的可靠推理**，强调通过精巧的架构设计和严谨的推理机制来弥补单纯扩大规模的不足。

### 重点方法深度解析
从所有批次中，以下四个工作因其清晰的创新思路和显著的实用价值而尤为突出：

**1. 《Pursuing Minimal Sufficiency in Spatial Reasoning》 (批次2)**
*   **核心创新点**：针对3D空间推理中“信息冗余”和“理解不足”的瓶颈，提出“最小充分性”原则，并设计了MSSR双智能体框架来系统性地实现该原则。
*   **技术细节**：框架包含**感知智能体**与**推理智能体**。感知智能体利用可编程的3D感知工具箱（含稳健的语言接地方向提取模块）提取“充分”信息；推理智能体则通过闭环迭代，不断修剪冗余、请求缺失，直至提炼出回答当前问题所必需的“最小充分信息集”。
*   **效果验证**：在具有挑战性的3D空间推理基准测试上取得了最先进的性能，显著提升了模型准确率。
*   **适用场景**：适用于需要基于3D场景（如机器人导航、AR/VR交互、自动驾驶）进行复杂、精确问答的任务，其可解释的推理路径还能为生成高质量训练数据提供支持。

**2. 《Uncertainty Quantification for Multimodal Large Language Models with Incoherence-adjusted Semantic Volume》 (批次1)**
*   **核心创新点**：提出了UMPIRE，一个无需训练、高效且通用的多模态大语言模型不确定性量化框架，旨在可靠地识别模型的错误输出。
*   **技术细节**：核心是计算“不协调调整的语义体积”。它通过对同一任务采样多个模型响应，一方面衡量响应的全局语义多样性（捕捉不确定性），另一方面评估每个响应内部基于模型置信度的局部不协调性（捕捉不可靠性）。
*   **效果验证**：在图像、音频、视频-文本等多种基准测试（包括对抗性和分布外设置）上，UMPIRE在错误检测和不确定性校准方面持续优于基线方法，并能泛化至生成任务。
*   **适用场景**：适用于任何需要评估MLLM输出可靠性、进行风险控制的场景，如医疗诊断辅助、自动驾驶决策、内容审核等，是实现可信AI部署的关键技术。

**3. 《From Intuition to Investigation: A Tool-Augmented Reasoning MLLM Framework for Generalizable Face Anti-Spoofing》 (批次1)**
*   **核心创新点**：将人脸活体检测任务重新定义为“带有视觉工具的思维链”范式，通过引入外部视觉工具辅助MLLM进行细粒度调查，显著提升了跨域泛化能力。
*   **技术细节**：提出的TAR-FAS框架允许模型从直观观察开始，自适应地调用外部视觉工具进行深入调查。为此，作者构建了包含多轮工具使用推理轨迹的数据集，并设计了多样工具组相对策略优化训练方法。
*   **效果验证**：在极具挑战性的跨域测试协议下取得了最先进的性能，同时其推理过程提供了可解释的细粒度视觉调查线索。
*   **适用场景**：为解决依赖细粒度、难以用语言直接描述的视觉模式识别问题提供了新范式，可扩展至工业缺陷检测、医学影像分析等领域。

**4. 《Dynamic Token Reweighting for Robust Vision-Language Models》 (批次2)**
*   **核心创新点**：提出了一种轻量级、推理时防御方法DTR，专门应对利用视觉-文本交互进行攻击的多模态“越狱”，首次成功将KV缓存优化技术应用于多模态模型的安全增强。
*   **技术细节**：将对抗性视觉输入引发的“安全相关分布偏移”进行数学形式化。在推理时动态调整视觉令牌在Key-Value缓存中的权重，从而抑制有害视觉信息的影响，无需修改模型权重。
*   **效果验证**：在多种视觉语言模型和攻击基准上的评估表明，DTR在有效防御攻击的同时，对模型在正常任务上的性能影响最小。
*   **适用场景**：对于任何部署在开放环境、需要防范恶意视觉输入攻击的视觉语言应用都至关重要，因其实现简单、几乎不增加推理开销，易于集成。

这些方法从不同维度构建了可靠多模态系统的基石：MSSR和TAR-FAS代表了**结构化、可解释的推理范式**，前者强调信息筛选，后者强调工具调用；UMPIRE和DTR则分别从**输出可靠性评估**和**输入安全性防御**两个角度保障了系统的鲁棒性。它们可以组合使用，例如，在部署一个用于工业质检的MLLM时，可结合TAR-FAS的框架进行推理，同时用UMPIRE评估其输出置信度，并用DTR防御潜在的对抗性输入。

### 实践启示
这些研究为多模态大模型的应用开发提供了清晰的路径。**首先，在追求性能的同时，必须将可靠性与安全性评估前置**。对于所有面向公众部署的服务，应集成类似UMPIRE的不确定性量化模块和DTR的轻量级防御方案。**其次，对于需要高精度、可解释推理的垂直领域（如工业、医疗）**，应优先考虑“大模型+专用工具/结构化推理”的混合架构，借鉴TAR-FAS或MSSR的思想，将黑盒预测转变为可控、可验证的流程。

具体落地建议包括：1）在新应用上线前，引入细粒度诊断工具评估模型短板，并建立不确定性量化和安全攻击测试流程。2）在资源受限或对成本敏感的场景下，优先关注推理时优化方法（如RAISE、DTR）或高效架构设计，以较低代价提升效果或安全性。3）构建评估体系时，不仅要关注单项指标，更要设计衡量复杂交互与推理能力的任务。

关键注意事项是：实现结构化推理框架时，需精心设计专家模块或工具调用逻辑，避免引入过高的复杂度和延迟。采用动态优化技术时，需结合自身业务数据进行充分的验证与调优，确保其有效性和稳定性。最佳实践组合是：针对高风险、高精度要求的应用，采用“结构化推理（TAR-FAS/MSSR）+ 不确定性评估（UMPIRE）+ 输入防御（DTR）”的综合方案，以实现性能、可解释性与安全性的平衡。

---

## 📄 论文列表（26 篇）

### [Uncertainty Quantification for Multimodal Large Language Models with Incoherence-adjusted Semantic Volume](https://arxiv.org/abs/2602.24195)

**作者**: Lau, Dao, Lin, Low  
**链接**: [arXiv](https://arxiv.org/abs/2602.24195) · [PDF](https://arxiv.org/pdf/2602.24195)  \| [📖 全文分析](paper_2602.24195.md)  
**评分**: 8.71  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.5）

> 本文提出了一种名为UMPIRE的训练免费不确定性量化框架，专门用于多模态大语言模型（MLLMs）。该方法通过计算采样响应的不协调调整语义体积，有效捕捉响应的全局语义多样性和基于模型内部置信度的局部不协调性。论文提供了理论分析，并通过在图像、音频和视频-文本基准（包括对抗性和分布外设置）上的广泛实验，证明了UMPIRE在错误检测和不确定性校准方面持续优于基线指标。作者还展示了UMPIRE向非文本输出任务（如图像和音频生成）的泛化能力。


### [SpinBench: Perspective and Rotation as a Lens on Spatial Reasoning in VLMs](https://arxiv.org/abs/2509.25390)

**作者**: Zhang, Corcodel, Hori, Cherian, Zhao  
**链接**: [arXiv](https://arxiv.org/abs/2509.25390) · [PDF](https://arxiv.org/pdf/2509.25390)  \| [📖 全文分析](paper_2509.25390.md)  
**评分**: 8.57  （novelty: 8.5 | method: 9.0 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为SpinBench的认知诊断基准，用于评估视觉语言模型（VLMs）的空间推理能力。该工作围绕视角转换这一核心挑战，设计了细粒度的诊断类别，系统评估了43个前沿VLM，揭示了模型在自我中心偏差、旋转理解等方面的系统性弱点。作者团队来自学术界（姓氏显示可能来自研究机构），但未明确标注知名机构。


### [Unified Vision-Language Modeling via Concept Space Alignment](https://arxiv.org/abs/2603.01096)

**作者**: Qiu, Duquenne, Schwenk  
**链接**: [arXiv](https://arxiv.org/abs/2603.01096) · [PDF](https://arxiv.org/pdf/2603.01096)  \| [📖 全文分析](paper_2603.01096.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 8.0）

> 本文由Meta AI（原Facebook AI Research）团队的研究人员提出，该团队在自然语言处理和多模态人工智能领域具有深厚的研究积累和影响力。论文提出了一种名为V-SONAR的视觉-语言统一嵌入空间构建方法，通过后验对齐管道将现有视觉编码器的表示映射到大规模多语言文本嵌入空间SONAR中，并在此基础上构建了视觉-语言大概念模型V-LCM。该方法在视频检索、视频描述生成和多语言视觉问答等任务上取得了具有竞争力的性能，特别是在低资源语言上表现突出。


### [InEdit-Bench: Benchmarking Intermediate Logical Pathways for Intelligent Image Editing Models](https://arxiv.org/abs/2603.03657)

**作者**: Sheng, Han, Zhang, Xiong, Ding 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.03657) · [PDF](https://arxiv.org/pdf/2603.03657)  \| [📖 全文分析](paper_2603.03657.md)  
**评分**: 8.57  （novelty: 9.0 | method: 9.0 | evidence: 8.0 | clarity: 8.5）

> 本文提出了首个专门用于评估图像编辑模型在中间逻辑路径推理能力的基准测试InEdit-Bench。该基准涵盖了状态转换、动态过程、时间序列和科学模拟四大任务类别，并设计了细粒度的评估标准来衡量生成路径的逻辑连贯性、视觉自然度以及对路径约束的忠实度。通过对14个代表性图像编辑模型的全面评估，揭示了当前模型在该领域的显著不足。这项工作为动态推理感知的多模态生成模型的发展提供了重要的评估工具和研究方向。


### [VPI-Bench: Visual Prompt Injection Attacks for Computer-Use Agents](https://arxiv.org/abs/2506.02456)

**作者**: Cao, Lim, Liu, Sui, Li 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2506.02456) · [PDF](https://arxiv.org/pdf/2506.02456)  \| [📖 全文分析](paper_2506.02456.md)  
**评分**: 8.50  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种针对计算机使用代理（CUAs）和浏览器使用代理（BUAs）的视觉提示注入（VPI）攻击基准VPI-Bench，系统性地研究了恶意指令通过视觉方式嵌入用户界面对AI代理安全性的威胁。论文通过构建包含306个测试用例的跨平台基准，实证发现当前代理在特定平台上受骗率高达51%（CUAs）和100%（BUAs），揭示了现有系统提示防御的局限性。研究强调了开发鲁棒、上下文感知防御机制的必要性，对多模态AI代理的安全部署具有重要警示意义。


### [From Intuition to Investigation: A Tool-Augmented Reasoning MLLM Framework for Generalizable Face Anti-Spoofing](https://arxiv.org/abs/2603.01038)

**作者**: Zhang, Wang, Zhang, Yue, Tan 等 14 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01038) · [PDF](https://arxiv.org/pdf/2603.01038)  \| [📖 全文分析](paper_2603.01038.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文提出了一种创新的工具增强推理多模态大语言模型框架（TAR-FAS），用于提升人脸活体检测（FAS）的泛化能力。该方法将FAS任务重新定义为带有视觉工具的思维链（CoT-VT）范式，通过引入外部视觉工具来辅助模型进行细粒度视觉模式调查，超越了仅依赖直观语义线索的传统方法。作者构建了包含16K样本的多轮工具使用推理轨迹数据集ToolFAS-16K，并设计了多样工具组相对策略优化（DT-GRPO）训练方法。在极具挑战性的1对11跨域协议下的实验表明，该方法取得了最先进的性能，同时提供了细粒度视觉调查以实现可信的欺骗检测。


### [From Narrow to Panoramic Vision: Attention-Guided Cold-Start Reshapes Multimodal Reasoning](https://arxiv.org/abs/2603.03825)

**作者**: Luo, Shi, Zhang, Yang, Jiang 等 13 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.03825) · [PDF](https://arxiv.org/pdf/2603.03825)  \| [📖 全文分析](paper_2603.03825.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文由阿里千问（Qwen）团队提出了一种针对多模态大语言模型冷启动阶段的新颖分析方法与优化框架。研究团队通过引入视觉注意力分数（VAS）这一量化指标，揭示了冷启动阶段存在的“注意力懒惰定位”现象，并基于此提出了AVAR（注意力引导的视觉锚定与反思）框架，在多个基准测试上显著提升了模型性能。该工作对理解多模态模型的训练动态具有重要理论价值，并提供了一套可操作的优化方案。


### [SaFeR-ToolKit: Structured Reasoning via Virtual Tool Calling for Multimodal Safety](https://arxiv.org/abs/2603.02635)

**作者**: Xu, He, Yi, Wang, Chen 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.02635) · [PDF](https://arxiv.org/pdf/2603.02635)  \| [📖 全文分析](paper_2603.02635.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 7.5）

> 本文提出了一种名为SaFeR-ToolKit的新方法，旨在解决多模态视觉语言模型在安全对齐中面临的越狱攻击和过度拒绝问题。该方法通过将安全决策过程形式化为一个可检查的协议，并引入虚拟工具调用机制来增强模型的安全推理能力。实验表明，该方法在Qwen2.5-VL模型上显著提升了安全性、有用性和推理严谨性，同时保持了模型的通用能力。代码已开源。


### [Seeing Beyond 8bits: Subjective and Objective Quality Assessment of HDR-UGC Videos](https://arxiv.org/abs/2603.00938)

**作者**: Saini, Chen, Birkbeck, Wang, Adsumilli 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.00938) · [PDF](https://arxiv.org/pdf/2603.00938)  \| [📖 全文分析](paper_2603.00938.md)  
**评分**: 8.43  （novelty: 9.0 | method: 8.0 | evidence: 9.5 | clarity: 8.0）

> 本文由德克萨斯大学奥斯汀分校（UT Austin）的Bovik教授团队（图像与视频工程实验室）提出，该团队在图像/视频质量评估领域享有盛誉。论文针对HDR用户生成视频质量评估这一新兴挑战，构建了大规模主观数据集Beyond8Bits，并提出了首个基于多模态大语言模型的HDR-UGC VQA方法HDR-Q，在创新性和实验规模方面表现突出。


### [FMint-SDE: A Multimodal Foundation Model for Accelerating Numerical Simulation of SDEs via Error Correction](https://arxiv.org/abs/2510.27173)

**作者**: Yuan, Yang, Cameron  
**链接**: [arXiv](https://arxiv.org/abs/2510.27173) · [PDF](https://arxiv.org/pdf/2510.27173)  \| [📖 全文分析](paper_2510.27173.md)  
**评分**: 8.43  （novelty: 9.0 | method: 9.0 | evidence: 8.0 | clarity: 8.0）

> 本文提出了一种新颖的多模态基础模型FMint-SDE，用于加速随机微分方程的数值模拟。该方法通过结合数值和文本模态，学习通用的误差校正方案，在多个应用领域（分子动力学、机械系统、金融、生物学）的基准测试中，相比传统求解器取得了更优的精度-效率权衡。作者团队背景信息未在提供内容中明确提及，故省略。


### [SleepLM: Natural-Language Intelligence for Human Sleep](https://arxiv.org/abs/2602.23605)

**作者**: Xu, Shuai, Mozaffari, Aysola, Kumar 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2602.23605) · [PDF](https://arxiv.org/pdf/2602.23605)  \| [📖 全文分析](paper_2602.23605.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为SleepLM的睡眠-语言基础模型，首次将自然语言处理与多模态睡眠生理信号分析相结合，实现了睡眠数据的语言对齐、解释和交互。论文通过创新的多级睡眠描述生成流程，构建了首个大规模睡眠-文本数据集（超过10万小时数据），并设计了统一的预训练目标。实验表明SleepLM在零样本/少样本学习、跨模态检索和睡眠描述生成等任务上优于现有方法，并展现出语言引导事件定位、针对性洞察生成等新兴能力。作者团队未明确标注知名机构，但研究内容具有前沿性。


### [Advancing Complex Video Object Segmentation via Progressive Concept Construction](https://arxiv.org/abs/2507.15852)

**作者**: Zhang, Ding, Dong, He, Lin 等 10 人  
**链接**: [arXiv](https://arxiv.org/abs/2507.15852) · [PDF](https://arxiv.org/pdf/2507.15852)  \| [📖 全文分析](paper_2507.15852.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Segment Concept (SeC)的概念驱动视频对象分割框架，通过从传统的特征匹配转向渐进式构建和利用高级、以对象为中心的表征，显著提升了复杂场景下的分割性能。作者团队来自多个研究机构，但未明确标注具体机构名称，因此省略团队背景介绍。该方法在提出的新基准SeCVOS和标准VOS基准上均大幅超越现有方法，创新性强，实验设计严谨。


### [Uni-X: Mitigating Modality Conflict with a Two-End-Separated Architecture for Unified Multimodal Models](https://arxiv.org/abs/2509.24365)

**作者**: Hao, Liu, Xiao, Huang, Yu  
**链接**: [arXiv](https://arxiv.org/abs/2509.24365) · [PDF](https://arxiv.org/pdf/2509.24365)  \| [📖 全文分析](paper_2509.24365.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为Uni-X的新型统一多模态模型架构，旨在解决模态共享自回归变换器中存在的视觉与文本梯度冲突问题。通过创新的两端分离、中间共享的X形设计，Uni-X在保持架构简洁性的同时，显著提升了训练效率和模型性能。实验表明，该模型在参数效率、可扩展性以及多模态理解与生成任务上均取得了优异成果。


### [StructXLIP: Enhancing Vision-language Models with Multimodal Structural Cues](https://arxiv.org/abs/2602.20089)

**作者**: Ruan, Gao, Kong, Wang, Cristani  
**链接**: [arXiv](https://arxiv.org/abs/2602.20089) · [PDF](https://arxiv.org/pdf/2602.20089)  \| [📖 全文分析](paper_2602.20089.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种增强视觉-语言模型的新方法StructXLIP，通过提取并跨模态对齐结构线索（如边缘图）来改进长文本、细节丰富的描述下的视觉-语言对齐，特别聚焦于提升跨模态检索性能。该方法在通用和特定领域的跨模态检索任务上均超越了现有方法，并可作为即插即用的通用增强方案集成到未来方法中。代码和预训练模型已开源。


### [RAISE: Requirement-Adaptive Evolutionary Refinement for Training-Free Text-to-Image Alignment](https://arxiv.org/abs/2603.00483)

**作者**: Jiang, Chen, Gao, Niu  
**链接**: [arXiv](https://arxiv.org/abs/2603.00483) · [PDF](https://arxiv.org/pdf/2603.00483)  \| [📖 全文分析](paper_2603.00483.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为RAISE的训练免费、需求驱动的进化框架，用于自适应文本到图像生成对齐。该方法将图像生成建模为需求驱动的自适应缩放过程，在推理时通过多样化的细化操作（包括提示重写、噪声重采样和指令编辑）进化候选图像群体，并根据结构化需求清单动态验证和分配计算资源。在GenEval和DrawBench基准测试中，RAISE实现了最先进的对齐性能（GenEval总体0.94），同时比先前的缩放和反射调优基线减少了30-40%的生成样本和80%的视觉语言模型调用，展示了高效、可泛化且模型无关的多轮自我改进能力。


### [AG-VAS: Anchor-Guided Zero-Shot Visual Anomaly Segmentation with Large Multimodal Models](https://arxiv.org/abs/2603.01305)

**作者**: Qu, Tao, Bao, Wang, Qu 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01305) · [PDF](https://arxiv.org/pdf/2603.01305)  \| [📖 全文分析](paper_2603.01305.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为AG-VAS（Anchor-Guided Visual Anomaly Segmentation）的新框架，用于解决基于大型多模态模型（LMMs）的零样本视觉异常分割（ZSAS）任务中的核心挑战。该方法通过引入可学习的语义锚点令牌和跨模态对齐模块，显著提升了抽象异常概念的视觉定位精度，并在六个工业和医学基准测试中取得了最先进的性能。


### [From Verbatim to Gist: Distilling Pyramidal Multimodal Memory via Semantic Information Bottleneck for Long-Horizon Video Agents](https://arxiv.org/abs/2603.01455)

**作者**: Lian, Wang, Yao, Wang, Chen 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.01455) · [PDF](https://arxiv.org/pdf/2603.01455)  \| [📖 全文分析](paper_2603.01455.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为MM-Mem的金字塔式多模态记忆架构，用于解决多模态大语言模型在长时程视频理解中的挑战。该方法受模糊痕迹理论启发，通过分层记忆结构（感官缓冲区、情景流、符号图式）和语义信息瓶颈目标，实现了从细粒度感知痕迹到高层语义图式的渐进蒸馏。在推理中采用基于熵的自顶向下记忆检索策略。在4个基准测试上的广泛实验验证了其有效性，代码已开源。


### [Through the Lens of Contrast: Self-Improving Visual Reasoning in VLMs](https://arxiv.org/abs/2603.02556)

**作者**: Pan, Wu, Hua, Feng, Yan 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.02556) · [PDF](https://arxiv.org/pdf/2603.02556)  \| [📖 全文分析](paper_2603.02556.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种新颖的视觉语言模型自改进框架VC-STaR，通过利用视觉对比来缓解模型生成推理链中的幻觉问题。该方法在多个VQA数据集上进行了广泛实验，显著提升了多种VLMs的推理能力，并开源了代码和构建的数据集VisCoR-55K。作者团队来自学术界，但未在摘要中明确标注其所属的知名机构。


### [PhyPrompt: RL-based Prompt Refinement for Physically Plausible Text-to-Video Generation](https://arxiv.org/abs/2603.03505)

**作者**: Wu, Xu, Xia, Li, Lu 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.03505) · [PDF](https://arxiv.org/pdf/2603.03505)  \| [📖 全文分析](paper_2603.03505.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种基于强化学习的提示词优化框架PhyPrompt，用于提升文本到视频生成模型的物理合理性。该方法通过两阶段训练：首先使用物理知识增强的思维链数据集微调大语言模型，然后采用分组相对策略优化与动态奖励课程，实现了语义保真度与物理常识的协同优化。在VideoPhy2基准上取得了显著提升，并展示了跨架构的零样本迁移能力。作者团队来自学术界，但未明确标注知名机构，因此省略团队背景介绍。


### [Traceable Evidence Enhanced Visual Grounded Reasoning: Evaluation and Methodology](https://arxiv.org/abs/2507.07999)

**作者**: Wang, Li, Huang, Wang, Wang 等 12 人  
**链接**: [arXiv](https://arxiv.org/abs/2507.07999) · [PDF](https://arxiv.org/pdf/2507.07999)  \| [📖 全文分析](paper_2507.07999.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种用于评估视觉基础推理能力的诊断性基准TreeBench，并开发了相应的训练范式TreeVGR。该研究填补了当前缺乏系统性评估视觉基础推理能力基准的空白，通过引入可追溯证据和二阶推理等原则，构建了具有挑战性的评估数据集。实验表明，即使是当前最先进的模型在该基准上表现也欠佳，而提出的TreeVGR方法在多个基准上取得了显著提升。作者团队来自学术界，但未明确标注具体机构，因此省略团队背景介绍。


### [Pursuing Minimal Sufficiency in Spatial Reasoning](https://arxiv.org/abs/2510.16688)

**作者**: Guo, Hou, Ma, Tang, Yang  
**链接**: [arXiv](https://arxiv.org/abs/2510.16688) · [PDF](https://arxiv.org/pdf/2510.16688)  \| [📖 全文分析](paper_2510.16688.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为MSSR（Minimal Sufficient Spatial Reasoner）的双智能体框架，旨在解决视觉语言模型在空间推理任务中的核心瓶颈。该方法通过构建最小充分信息集，并引入新颖的SOG模块来稳健地提取语言接地方向，在两个具有挑战性的基准测试中实现了最先进的性能。论文实验设计充分，代码已开源，为空间推理领域提供了可解释的解决方案和高质量的训练数据来源。


### [SceneCOT: Eliciting Grounded Chain-of-Thought Reasoning in 3D Scenes](https://arxiv.org/abs/2510.16714)

**作者**: Linghu, Huang, Zhu, Jia, Huang  
**链接**: [arXiv](https://arxiv.org/abs/2510.16714) · [PDF](https://arxiv.org/pdf/2510.16714)  \| [📖 全文分析](paper_2510.16714.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为SceneCOT的新框架，首次将链式思维（Chain-of-Thought）推理方法应用于3D场景理解任务。该方法通过将复杂推理任务分解为更简单的子问题，并利用多模态专家模块构建视觉线索，实现了基于3D场景的、可解释的逐步推理。作者团队来自学术界，但未在摘要中明确标注其所属机构是否为知名高校或研究机构，因此省略团队背景介绍。


### [Grasp Any Region: Towards Precise, Contextual Pixel Understanding for Multimodal LLMs](https://arxiv.org/abs/2510.18876)

**作者**: Wang, Wang, Zhang, Zhou, Li 等 16 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.18876) · [PDF](https://arxiv.org/pdf/2510.18876)  \| [📖 全文分析](paper_2510.18876.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为Grasp Any Region (GAR)的新方法，旨在解决多模态大语言模型在区域级视觉理解中忽略全局上下文的问题。通过引入RoI对齐的特征重放技术，GAR实现了精确感知、多提示交互建模和高级组合推理。作者构建了GAR-Bench评估基准，并在多个数据集上进行了广泛实验，结果表明GAR在区域描述、多区域关系理解和视频理解任务上均取得了优异性能，甚至在某些基准上超越了规模大得多的模型。


### [NeuCLIP: Efficient Large-Scale CLIP Training with Neural Normalizer Optimization](https://arxiv.org/abs/2511.08417)

**作者**: Wei, Lin, Yang  
**链接**: [arXiv](https://arxiv.org/abs/2511.08417) · [PDF](https://arxiv.org/pdf/2511.08417)  \| [📖 全文分析](paper_2511.08417.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为NeuCLIP的新型优化框架，用于解决大规模CLIP训练中归一化项估计的计算难题。该方法通过凸分析和变分分析将对比损失重新表述，并引入辅助神经网络来预测对数归一化器，从而在减少计算资源需求的同时提高了估计精度。在大规模数据集（从百万到十亿级别样本）上的实验表明，NeuCLIP优于现有方法。论文代码已开源。


### [Dynamic Token Reweighting for Robust Vision-Language Models](https://arxiv.org/abs/2505.17132)

**作者**: Jiang, Liang, Zhu, Zhou, Ma 等 6 人  
**链接**: [arXiv](https://arxiv.org/abs/2505.17132) · [PDF](https://arxiv.org/pdf/2505.17132)  \| [📖 全文分析](paper_2505.17132.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为DTR的新型推理时防御方法，通过优化视觉语言模型的关键值缓存来缓解多模态越狱攻击。该方法创新性地将视觉模态引发的安全相关分布偏移进行形式化，并动态调整视觉令牌权重，在保持模型通用能力和推理效率的同时最小化对抗性视觉输入的影响。在多个视觉语言模型和攻击基准上的广泛评估表明，DTR在攻击鲁棒性和良性任务性能方面均优于现有防御方法，标志着KV缓存优化在多模态基础模型安全增强中的首次成功应用。


### [MedGPT-oss: Training a General-Purpose Vision-Language Model for Biomedicine](https://arxiv.org/abs/2603.00842)

**作者**: Zhang, Yuan, Peng, Zhao, Lyu 等 13 人  
**链接**: [arXiv](https://arxiv.org/abs/2603.00842) · [PDF](https://arxiv.org/pdf/2603.00842)  \| [📖 全文分析](paper_2603.00842.md)  
**评分**: 8.36  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为MEDGPT-OSS的开源、参数高效（20B）的通用生物医学视觉语言模型。该研究旨在解决高性能生物医学多模态助手普遍存在的闭源或计算成本过高的问题，从而满足医疗场景下对患者隐私和数据合规（如PHI）的严格要求。通过采用优化的三阶段训练课程，将GPT-oss语言主干与视觉前端相结合，并在严格的数据筛选和长上下文多模态对齐下进行渐进式领域适应，证明了较小模型也能弥合能力差距。该模型在分布外多模态推理和复杂纯文本临床任务上超越了更大的开源医学模型，并统一了多种模态的指令跟随接口。作者团队（Zhang, Yuan, Peng等）未明确标注所属机构，但从技术路线和问题定位来看，很可能来自专注于AI与医疗交叉领域的研究团队。

