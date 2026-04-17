# Multimodal · 2026年04月15日

**论文数**: 10 篇

---

## 📊 趋势分析

### 研究全貌

本批次多模态领域的研究论文主要聚焦于**提升模型在复杂任务中的感知与推理能力**，并探索**高效的数据构建与模型训练范式**。具体研究方向包括：1）**视觉-代码智能**，旨在弥合程序逻辑与其视觉输出之间的鸿沟；2）**多模态推理优化**，通过改进强化学习算法或模型架构，增强模型对视觉、音频等非文本模态的深度理解；3）**鲁棒性增强**，针对语音、视频等模态中的噪声、幻觉或语义固化问题进行专项改进；4）**统一框架与数据合成**，构建能够同时处理多种任务或自动生成高质量训练数据的系统。当前的热点问题是如何让大语言模型更可靠、更深入地“理解”并“利用”非文本信息，而不仅仅是将其转录为文本。整体研究趋势呈现出从单一任务模型向统一、多功能框架演进，并更加注重通过算法创新（而非单纯增加数据或模型规模）来解决感知与推理的瓶颈。

### 重点方法深度解析

从这批论文中，以下几个工作因其创新性和实用性尤为突出：

**1. 《JanusCoder: Towards a Foundational Visual-Programmatic Interface for Code Intelligence》 [URL](https://arxiv.org/abs/2510.23538)
*   **核心创新点**：该研究突破了传统代码智能仅关注文本源码的局限，致力于建立视觉与程序代码之间的双向桥梁。其核心是解决了高质量多模态代码数据稀缺的瓶颈，并基于此训练了统一的视觉-程序化接口模型。
*   **技术细节**：方法包含两大支柱。首先，开发了一套**数据合成工具包**，利用代码执行生成视觉输出（如图表、UI、动画），再通过视觉内容反向验证和优化代码质量，从而高效构建了包含80万样本的JanusCode-800K语料库。其次，基于此语料库训练了**JanusCoder系列模型**（7B至14B规模），该模型能够接受文本指令、视觉截图或两者结合作为输入，并生成相应的代码。它采用统一的模型架构，而非为不同任务训练专用模型。
*   **效果验证**：在文本编码（如HumanEval）和视觉编码任务上均表现出色，其性能接近甚至超过了一些商业模型，证明了大规模高质量合成数据与统一建模的有效性。
*   **适用场景**：适用于需要根据视觉设计稿生成前端代码、根据图表生成绘图脚本，或进行程序驱动的视觉内容编辑等场景。

**2. 《Perception-Aware Policy Optimization for Multimodal Reasoning》 [URL](https://arxiv.org/abs/2507.06448)
*   **核心创新点**：针对现有基于可验证奖励的强化学习（RLVR）在多模态推理中因感知能力不足而表现不佳的问题，提出了一种感知感知的策略优化算法（PAPO），将“学习感知”与“学习推理”深度融合。
*   **技术细节**：PAPO的核心是在标准RLVR目标（如GRPO, DAPO）中引入了一个**隐式感知损失**，该损失以KL散度的形式，鼓励模型在推理过程中保持对原始视觉输入的忠实理解。为了稳定训练，还引入了**双重熵损失**进行正则化。该方法无需额外数据、奖励模型或更强的教师模型。
*   **效果验证**：在多个多模态基准测试上，PAPO带来了4.4%-17.5%的整体性能提升，在视觉依赖度高的任务上提升可达19.1%，同时将感知错误降低了30.5%。
*   **适用场景**：适用于任何需要多步视觉推理的任务，如基于图表或网页截图进行问答、规划等。

**3. 《Don‘t Show Pixels, Show Cues: Unlocking Visual Tool Reasoning in Language Models via Perception Programs》 [URL](https://arxiv.org/abs/2604.12896)
*   **核心创新点**：该工作洞察到，为多模态大模型提供原始视觉工具（如深度图、光流图）的输出效果有限，因为密集的像素表示与LLM的语言原生推理优势不匹配。因此，提出了一种名为“感知程序”（P²）的**训练无关、模型无关**的中间表示方法。
*   **技术细节**：P²的核心思想是**重写工具输出**。它将原始的、高维的像素级工具输出，转换或总结为紧凑、结构化、语言化的描述（例如，“物体A在物体B的左前方”），然后再输入给MLLM进行推理。这个过程本身不训练模型，只是改变了输入给模型的信息形式。
*   **效果验证**：在BLINK基准的六个感知中心任务上，P²为不同规模的基座模型带来了巨大提升。例如，使用GPT-5 Mini作为基座，在多视图推理任务上准确率从41.35%提升至86.47%。即使在小模型（如InternVL3.5-4B）上也能实现15-40%的绝对增益，超越了需要训练的代理或强化学习方法。
*   **适用场景**：适用于任何需要集成外部视觉工具（如OpenCV函数、预计算的特征图）来辅助复杂空间、几何或运动推理的应用。

### 实践启示

这批研究为多模态大模型的应用开发提供了清晰的路径。**对于需要从视觉输入生成代码或结构化输出的场景（如低代码平台、自动化测试），JanusCoder的工作展示了通过合成数据构建统一接口的巨大潜力。**对于追求更高推理精度和鲁棒性的复杂任务（如智能客服、教育辅导），PAPO算法提供了一种无需昂贵标注即可提升模型感知能力的有效范式。**而P²方法则极具通用性，它启示我们，在为大模型集成工具时，关键在于设计一个“翻译层”，将工具的专业输出“翻译”成大模型易于理解和处理的自然语言或结构化提示，这能极大释放现有模型的潜力，且实施成本低。

**可落地的具体建议**：1）在开发视觉编程工具时，可借鉴JanusCoder的数据合成思路，构建领域特定的视觉-代码对数据集。2）在微调模型进行决策或规划类任务时，可尝试将PAPO的隐式感知损失思想融入训练目标。3）**最关键且易实施的建议是采纳P²的思想**：在调用任何外部API或工具后，不要直接将原始结果（如JSON、图像矩阵）扔给大模型，而是先编写一个轻量的“摘要器”模块，将结果转化为简洁的文本描述，这通常能带来立竿见影的效果提升。

**关键注意事项**：使用合成数据时，需警惕与真实分布的偏差，应配合一定量的真实数据进行混合训练或评估。应用PAPO等强化学习方法时，需注意超参数调优以保持训练稳定性。而采用P²这类提示工程方法时，需精心设计摘要的格式和内容，确保其信息完整且无歧义。

---

## 📄 论文列表（10 篇）

### [JanusCoder: Towards a Foundational Visual-Programmatic Interface for Code Intelligence](https://arxiv.org/abs/2510.23538)

**作者**: Sun, Gong, Liu, Chen, Li 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2510.23538) · [PDF](https://arxiv.org/pdf/2510.23538)  \| [📖 全文分析](paper_2510.23538.md)  
**评分**: 8.73  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由InternLM团队（上海人工智能实验室）提出了一种名为JanusCoder的视觉-程序化接口基础模型，用于代码智能任务。该研究从数据和模型两个角度做出贡献：首先开发了一套完整的合成工具包，构建了目前最大的多模态代码语料库JanusCode-800K；然后基于此训练了JanusCoder系列模型，建立了统一的视觉-程序化接口，能够从文本指令、视觉输入或两者结合生成代码。在文本中心和视觉中心的编码任务上均表现出优越性能，7B至14B规模的模型接近甚至超过商业模型性能。


### [Perception-Aware Policy Optimization for Multimodal Reasoning](https://arxiv.org/abs/2507.06448)

**作者**: Wang, Guo, Stoica, Xu, Wang 等 11 人  
**链接**: [arXiv](https://arxiv.org/abs/2507.06448) · [PDF](https://arxiv.org/pdf/2507.06448)  \| [📖 全文分析](paper_2507.06448.md)  
**评分**: 8.69  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 7.5）

> 本文提出了一种名为PAPO（Perception-Aware Policy Optimization）的新型策略梯度算法，旨在解决多模态推理任务中视觉感知能力不足的瓶颈。该方法通过引入隐式感知损失（Implicit Perception Loss）和双重熵损失（Double Entropy Loss），在不依赖额外数据、奖励模型或更强教师模型的情况下，显著提升了多模态大语言模型的推理性能。在多个多模态基准测试中取得了4.4%-17.5%的整体提升，在视觉依赖度高的任务上提升更为显著（8.0%-19.1%），同时将感知错误降低了30.5%。代码和数据将开源。


### [Beyond Transcription: Unified Audio Schema for Perception-Aware AudioLLMs](https://arxiv.org/abs/2604.12506)

**作者**: Zhang, Song, Liu, Wu, Zhang 等 9 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.12506) · [PDF](https://arxiv.org/pdf/2604.12506)  \| [📖 全文分析](paper_2604.12506.md)  
**评分**: 8.64  （novelty: 9.0 | method: 8.5 | evidence: 9.5 | clarity: 8.0）

> 本文由腾讯团队提出了一种新的音频大语言模型监督框架——统一音频模式（UAS），旨在解决当前AudioLLMs在复杂推理任务上表现出色，但在细粒度声学感知上表现不佳的性能倒置问题。该方法通过将音频信息结构化地组织为转录、副语言信息和非语言事件三个明确组件，实现了全面的声学覆盖，同时在多个基准测试上取得了显著提升，代码和模型已开源。


### [StableToken: A Noise-Robust Semantic Speech Tokenizer for Resilient SpeechLLMs](https://arxiv.org/abs/2509.22220)

**作者**: Song, Zhang, Wu, Liu, Jia 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2509.22220) · [PDF](https://arxiv.org/pdf/2509.22220)  \| [📖 全文分析](paper_2509.22220.md)  
**评分**: 8.50  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文由腾讯团队提出了一种名为StableToken的新型语义语音分词器，旨在解决现有语音分词器对无关声学扰动（如噪声）的脆弱性问题。作者发现，即使在信噪比很高、语音清晰可懂的情况下，现有分词器的输出序列也可能发生剧烈变化，这增加了下游大语言模型的学习负担。StableToken通过引入共识驱动机制，采用多分支并行处理架构和强大的比特级投票机制，显著提升了分词稳定性，并在下游语音大语言模型的鲁棒性上取得了直接收益。论文创新性突出，实验证据充分，代码已开源。


### [Towards Long-horizon Agentic Multimodal Search](https://arxiv.org/abs/2604.12890)

**作者**: Du, Liu, Peng, Wu, Li 等 8 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.12890) · [PDF](https://arxiv.org/pdf/2604.12890)  \| [📖 全文分析](paper_2604.12890.md)  
**评分**: 8.43  （novelty: 8.5 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文由RUCAIBox团队（作者来自中国人民大学高瓴人工智能学院等机构）提出了一种面向长视野的多模态深度搜索框架LMM-Searcher。该方法通过文件化视觉表示机制和渐进式视觉加载策略，有效解决了长视野多模态搜索中的上下文爆炸和视觉信号丢失问题，在多个基准测试中实现了最先进的性能。


### [Distorted or Fabricated? A Survey on Hallucination in Video LLMs](https://arxiv.org/abs/2604.12944)

**作者**: Huang, Zhang, Wang, Zhang, Shi 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.12944) · [PDF](https://arxiv.org/pdf/2604.12944)  \| [📖 全文分析](paper_2604.12944.md)  
**评分**: 8.43  （novelty: 7.0 | method: 8.0 | evidence: 8.5 | clarity: 9.0）

> 本文是一篇关于视频大语言模型幻觉问题的综述论文，系统性地分析了Vid-LLMs中幻觉现象的类型、评估方法、缓解策略及未来研究方向。论文对动态失真和内容伪造两种核心幻觉类型进行了详细分类，并探讨了其根本原因（如时序表征能力有限和视觉基础不足），为构建鲁棒可靠的视频-语言系统提供了理论基础。作者团队来自学术界，但未明确标注知名机构，因此省略团队背景介绍。


### [All in One: A Unified Synthetic Data Pipeline for Multimodal Video Understanding](https://arxiv.org/abs/2604.12335)

**作者**: Rahman, Liao, Sigal  
**链接**: [arXiv](https://arxiv.org/abs/2604.12335) · [PDF](https://arxiv.org/pdf/2604.12335)  \| [📖 全文分析](paper_2604.12335.md)  
**评分**: 8.43  （novelty: 8.5 | method: 9.0 | evidence: 8.0 | clarity: 8.0）

> 本文提出了一种统一的多模态视频理解合成数据生成框架，旨在解决真实世界视频数据标注成本高、多样性有限的问题。该框架能够自动生成无限量的多模态视频数据，并支持多种任务格式。通过引入基于VQA的微调策略，模型能够进行更深层次的视觉推理。实验表明，主要使用合成数据训练的模型在视频对象计数、视频问答和视频对象分割等任务上能够有效泛化到真实数据集，性能优于传统训练方法。作者团队信息未在摘要中明确提及，故省略背景介绍。


### [IAD-Unify: A Region-Grounded Unified Model for Industrial Anomaly Segmentation, Understanding, and Generation](https://arxiv.org/abs/2604.12440)

**作者**: Zheng, Lin, Wang, Wang, Zhang 等 7 人  
**链接**: [arXiv](https://arxiv.org/abs/2604.12440) · [PDF](https://arxiv.org/pdf/2604.12440)  \| [📖 全文分析](paper_2604.12440.md)  
**评分**: 8.36  （novelty: 9.0 | method: 8.5 | evidence: 8.5 | clarity: 8.0）

> 本文提出了一种名为IAD-Unify的统一框架，旨在解决工业异常检测（IAD）中分割、理解和生成三大任务的联合处理难题。该工作通过冻结的DINOv2区域专家与轻量级令牌注入技术，将精确的异常证据提供给共享的Qwen3.5-4B视觉语言骨干网络，实现了多任务的统一建模。为支持统一评估，作者构建了包含近6万张图像的大规模多任务评估平台Anomaly-56K。实验表明，区域接地机制对理解任务至关重要，且模型在未见类别上表现出良好的跨类别泛化能力。


### [Don't Show Pixels, Show Cues: Unlocking Visual Tool Reasoning in Language Models via Perception Programs](https://arxiv.org/abs/2604.12896)

**作者**: Janjua, Silva, Niu, Rashidi  
**链接**: [arXiv](https://arxiv.org/abs/2604.12896) · [PDF](https://arxiv.org/pdf/2604.12896)  \| [📖 全文分析](paper_2604.12896.md)  
**评分**: 8.12  （novelty: 9.0 | method: 9.0 | evidence: 8.5 | clarity: 7.5）

> 本文提出了一种名为Perception Programs（P²）的训练无关、模型无关的方法，旨在解决多模态语言模型（MLLMs）在利用视觉工具（如深度、光流、对应关系）输出进行推理时存在的瓶颈。该方法的核心创新在于将原始、密集的像素级工具输出，重写为紧凑、结构化、语言原生的摘要，从而与LLMs的语言原生推理优势对齐。在BLINK基准的六个感知中心任务上，P²方法相比基线模型和原始工具增强基线取得了显著且一致的性能提升，甚至在小规模MLLMs上也实现了15-40%的绝对增益，超越了先前的基于代理、监督和强化学习的工具使用方法，且无需任何训练或模型修改。作者团队来自学术界，但具体机构信息未在提供内容中明确标注，故省略团队背景介绍。


### [Beyond Perception Errors: Semantic Fixation in Large Vision-Language Models](https://arxiv.org/abs/2604.12119)

**作者**: Alam  
**链接**: [arXiv](https://arxiv.org/abs/2604.12119) · [PDF](https://arxiv.org/pdf/2604.12119)  \| [📖 全文分析](paper_2604.12119.md)  
**评分**: 7.85  （novelty: 9.0 | method: 8.5 | evidence: 9.0 | clarity: 8.0）

> 本文针对大型视觉语言模型（VLMs）中存在的语义固化现象进行了深入研究，提出了一个名为VLM-Fix的基准测试来分离感知错误与规则映射错误。论文通过设计抽象策略游戏实验，揭示了VLMs在面对标准规则与逆规则时表现出的系统性偏差，并通过提示干预、后训练和激活导向等方法验证了语义固化机制的存在和可编辑性。作者团队来自学术界（论文分类为cs.CV, cs.LG），但未明确标注知名机构，因此省略团队背景介绍。

