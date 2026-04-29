# Qwen3.5-Omni Technical Report

**arXiv**: [2604.15804](https://arxiv.org/abs/2604.15804) · [PDF](https://arxiv.org/pdf/2604.15804)  
**领域**: Pretraining  
**作者**: Qwen Team  
**综合评分**: 8.67  （novelty: 9.0 · method: 9.0 · evidence: 8.5 · clarity: 7.5）

---

## 摘要

> 本文由阿里千问（Qwen）团队提出，该团队是阿里巴巴在人工智能领域的重要研究力量，专注于大语言模型和多模态技术研发。论文介绍了Qwen3.5-Omni模型的最新进展，这是一个参数规模达数千亿、支持256K上下文长度的多模态大模型。模型在音频和音视频理解、推理与交互的215个子任务和基准测试中取得了SOTA结果，超越了Gemini-3.1 Pro在关键音频任务上的表现，并在综合音视频理解上与之匹敌。论文创新性地提出了用于流式语音合成的ARIA技术，并展示了模型在音视频编程等新兴能力上的突破。整体工作技术先进，实验验证充分，代表了当前多模态大模型的前沿水平。

---

## 详细分析

> **社区热度**: ⭐ 22 (来自 papers.cool)

## 问题定义

这篇论文试图解决**构建原生全模态（native omnimodal）大语言模型**所面临的一系列核心挑战，具体包括：

## 1. 被动感知到主动具身智能的范式转变
现有模型多局限于被动感知-响应范式，缺乏可扩展的具身行为（scalable agentic behavior）能力。Qwen3.5-Omni 旨在突破这一限制，实现：
- **自主工具调用**：原生支持 WebSearch、复杂 FunctionCall 和代码执行
- **实时决策**：基于音频-视觉指令直接生成可执行代码（Audio-Visual Vibe Coding），无需外部编排即可响应实时查询
- **端到端控制**：支持对音量、语速和情感的端到端语音控制

## 2. 超长上下文的多模态建模
针对长序列音视频理解的需求，解决上下文长度受限的问题：
- 支持 **256k 上下文长度**，可处理超过 **10 小时**的音频或 **400 秒**的 720P 视频（1 FPS）
- 采用显式时间戳（explicit timestamps）和 TM-RoPE 技术，改善长距离时序建模，避免传统方法中时间位置 ID 过于稀疏导致的性能衰减

## 3. 流式实时交互的稳定性与效率
针对流式语音生成中的关键痛点：
- **文本-语音单元不对齐**：提出 **ARIA（Adaptive Rate Interleave Alignment）**技术，动态对齐文本和语音单元，解决因编码效率差异导致的跳词、发音错误和数字渲染歧义等问题
- **低延迟推理**：通过混合注意力专家混合（Hybrid Attention MoE）架构、分块预填充（chunked prefill）和流式 Talker 设计，实现首包延迟低至 235ms（Flash 版本）和 435ms（Plus 版本）

## 4. 多语言与跨语言语音生成的鲁棒性
扩展模型的语言覆盖范围并提升语音合成质量：
- 支持 **113 种语言和方言**的语音识别，以及 **36 种语言**的语音合成
- 实现零样本语音克隆（zero-shot voice customization）和跨语言语音生成（cross-lingual voice cloning），在保持说话人特征的同时确保目标语言的准确性

## 5. 细粒度音视频理解与结构化输出
提升模型对复杂音视频内容的结构化理解能力：
- 生成**剧本级结构化字幕**（script-level structured captions），包含精确的时间同步和自动场景分割
- 增强音频-视觉定位（audio-visual grounding）能力，精确描述角色及其与音频的关系

简言之，该论文致力于构建一个**既能感知推理、又能实时交互和自主行动的统一全模态系统**，在保持文本和视觉能力不降级的前提下，显著扩展音频和音视频理解的能力边界。

## 相关工作

根据论文内容，相关研究主要涵盖以下几个领域：

## 1. 大规模语言模型（LLMs）
- **GPT系列**：Brown et al. (2020) 提出语言模型的小样本学习能力；OpenAI (2023) 发布GPT-4技术报告
- **Claude系列**：Anthropic (2023a;b; 2024) 介绍Claude系列模型的发展
- **Qwen系列**：Bai et al. (2023a) 发布Qwen技术报告；Yang et al. (2024a; 2025a) 分别提出Qwen2和Qwen3
- **Llama系列**：Touvron et al. (2023) 发布Llama 2；Dubey et al. (2024) 介绍Llama 3模型家族

## 2. 视觉-语言模型
- **BLIP-2**：Li et al. (2023) 通过冻结图像编码器和大型语言模型引导视觉-语言预训练
- **LLaVA**：Liu et al. (2023) 提出视觉指令微调方法
- **MiniGPT-4**：Zhu et al. (2023) 使用先进的大型语言模型增强视觉-语言理解
- **Qwen-VL系列**：Bai et al. (2023b; 2025a) 开发Qwen-VL和Qwen2.5-VL模型

## 3. 音频理解与语音处理
- **Qwen-Audio系列**：Chu et al. (2023; 2024) 构建统一的大规模音频-语言模型Qwen-Audio和Qwen2-Audio，实现通用音频理解

## 4. 原生全模态系统（Native Omnimodal Systems）
- **GPT-4o**：OpenAI (2024) 发布支持文本、音频、视觉的端到端多模态模型
- **Gemini系列**：Comanici et al. (2025) 介绍Gemini 2.5的推理、多模态和长上下文能力；Gemini Team (2024) 发布Gemini 1.5技术报告
- **Qwen-Omni系列**：Xu et al. (2025a;b) 分别提出Qwen2.5-Omni和Qwen3-Omni，采用Thinker-Talker架构实现全模态理解与生成

## 5. 语音合成与生成技术
- **Seed-TTS**：Anastassiou et al. (2024) 开发高质量多样化语音生成模型家族
- **掩码生成方法**：Wang et al. (2024c) 提出MaskGCT，基于掩码生成编解码器Transformer实现零样本TTS
- **流匹配与扩散模型**：Eskimez et al. (2024) 提出E2 TTS；Chen et al. (2024c) 提出F5-TTS
- **专用语音模型**：Wang et al. (2025b) 提出Spark TTS；Du et al. (2024b; 2025) 开发CosyVoice 2和CosyVoice 3；Zhang et al. (2025a) 提出MiniMax-Speech；Zhang et al. (2025c) 提出MiMo-Audio-7B

## 6. 训练与优化方法
- **直接偏好优化（DPO）**：Rafailov et al. (2023) 提出无需奖励模型的偏好优化算法
- **组序列策略优化（GSPO）**：Zheng et al. (2025) 提出改进的训练稳定性方法
- **对话格式**：OpenAI (2022) 提出ChatML格式用于对话数据组织

## 7. 评估基准（Benchmarks）
论文在评估部分广泛引用了各类基准测试，包括：
- **文本理解**：MMLU-Pro (Wang et al., 2024d)、SuperGPQA (Team et al., 2025)、C-Eval (Huang et al., 2023)
- **音频理解**：MMAU (Sakshi et al., 2024)、MMAR (Ma et al., 2025a)、VoiceBench (Chen et al., 2024b)
- **语音识别**：Fleurs (Conneau et al., 2022)、Common Voice (Ardila et al., 2020)、LibriSpeech (Panayotov et al., 2015)
- **视觉理解**：MMMU (Yue et al., 2023)、MathVista (Lu et al., 2024)、Video-MME (Fu et al., 2024)
- **音视频理解**：DailyOmni (Zhou et al., 2025b)、WorldSense (Hong et al., 2025)、Qualcomm IVD (Pourreza et al., 2025)

## 解决方案

论文通过以下关键技术方案解决原生全模态大语言模型的构建难题：

## 1. Thinker–Talker 架构的演进与 MoE 扩展

**核心架构**：
- 沿用并改进 **Thinker–Talker 架构**：Thinker 负责多模态感知与文本生成，Talker 专司流式语音生成，直接从 Thinker 接收高级表征进行解码
- **混合专家混合（Hybrid Attention MoE）设计**：Thinker 和 Talker 均采用 Hybrid MoE 架构，引入 **Gated Delta Net (GDN)** 模块，显著降低长序列推理中的 KV-cache I/O 开销，提升并发处理能力

**输入处理**：
- **Audio Transformer (AuT)**：基于 Transformer 的音频编码器，使用 40M 小时音频-文本对训练，通过 4 个 Conv2D 块将滤波器组特征降采样 16 倍，输出 6.25Hz 的音频 token 率
- **视觉编码器**：采用 Qwen3.5 的视觉编码器处理图像和视频，支持动态帧率采样

## 2. 超长上下文建模机制

**位置编码策略**：
- **TM-RoPE 改进**：针对长视频或音视频输入中时间位置 ID 过于稀疏的问题，在每个视频或音视频时间块前添加**显式时间戳**（格式化为秒级的文本字符串）
- **随机间隔插入**：在音频序列中随机间隔插入时间戳，增强跨模态时间对齐能力
- **连续位置编号**：处理多模态时，确保位置编号连续，避免模态间位置冲突

**上下文规模**：
- 支持 **256k tokens** 的上下文长度，可处理超过 **10 小时**的音频或 **400 秒**的 720P 视频（1 FPS）

## 3. 流式语音生成的稳定性保障（ARIA）

**问题诊断**：
- 识别出流式语音合成不稳定性的根源：文本与语音分词器的**编码效率差异**导致单元不对齐

**解决方案——ARIA（Adaptive Rate Interleave Alignment）**：
- **动态速率约束**：将传统的双轨生成范式统一为单流交错格式，要求对于生成序列的任何前缀，累积的语音-文本 token 比率不得超过对应项目级的全局比率
- **统一交错流**：在流式解码过程中动态对齐文本和语音单元，避免跳词、发音错误和数字渲染歧义，同时最小化延迟影响

**语音表示**：
- 采用基于 **RVQ（Residual Vector Quantization）**的多码本编解码器表示，支持单帧即时合成
- **多 token 预测（MTP）模块**：建模残差码本，实现细粒度的声学细节建模和控制

## 4. 三阶段预训练策略

**阶段一：编码器对齐（S1）**
- 锁定 LLM 参数，单独训练视觉和音频编码器及其适配器，使用大规模音频-文本和图像-文本对增强语义理解

**阶段二：通用训练（S2）**
- 解冻所有参数，使用约 4T tokens 的多模态数据（文本 0.92T、音频 1.99T、图像 0.95T、视频 0.14T、视频-音频 0.29T）进行全面学习，序列长度 32k

**阶段三：长上下文优化（S3）**
- 将最大 token 长度扩展至 262k，增加长音频和长视频数据比例，显著提升长序列理解能力

## 5. 后训练优化（Post-training）

**Thinker 的三阶段后训练**：
- **专家蒸馏**：训练各领域（文本、视觉、音频）专门的教师模型，将专业能力蒸馏到统一模型
- **在线策略蒸馏**：针对音频查询响应质量低于文本查询的问题，使用文本条件下的高质量响应作为音频查询的蒸馏目标，逐步对齐音频与文本条件下的输出质量
- **交互对齐强化学习**：构建多轮交互轨迹，设计针对语言代码切换、角色一致性和长上下文指令遵循的奖励信号，优化实际对话体验

**Talker 的四阶段训练**：
- **通用阶段**：使用 20M+ 小时多语言语音数据，引入指令跟随语音生成等多样化任务
- **长上下文阶段**：通过数据质量分层和持续预训练（CPT），缓解噪声数据导致的幻觉，将上下文扩展至 64k tokens
- **强化学习阶段**：采用 DPO 和 GSPO（Group Sequence Policy Optimization），基于人工标注的多语言偏好对进行优化
- **说话人微调阶段**：轻量级微调实现零样本语音克隆和可控语音生成

## 6. 低延迟流式推理设计

**分块预填充（Chunked Prefill）**：
- 音频和视觉编码器沿时间维度输出分块，显著降低 Thinker 和 Talker 的 Time-To-First-Token（TTFT）

**并发优化**：
- 使用内部 vLLM 配合 torch.compile 和 CUDA Graph 加速，MTP 模块和编解码器解码器计算轻量且支持批处理
- Flash 版本首包延迟低至 **235ms**（音频）和 **426ms**（视频），Plus 版本为 **435ms** 和 **651ms**

**实时交互能力**：
- 支持语义打断（semantic interruption）和原生轮次转换意图识别
- 支持通过用户提供的样本进行零样本语音克隆，以及音量、语速和情感的端到端控制

## 7. 多语言与跨语言扩展

**数据扩展**：
- 训练数据涵盖 **113 种语言和方言**的语音识别，以及 **36 种语言**的语音合成
- 音频编码器训练采用 3.5 : 3.5 : 3 的中英多语言数据比例

**能力涌现**：
- 支持跨语言语音克隆（如中文输入克隆到日语或韩语输出），在保持说话人特征的同时确保目标语言准确性
- 实现可控的音视频字幕生成，包含精确时间同步和自动场景分割的剧本级描述

通过这些技术整合，Qwen3.5-Omni 实现了从被动感知到主动具身智能的转变，在 215 个音频和音视频基准测试中达到 SOTA 性能，同时保持了与同规模 Qwen 模型相当的文本和视觉能力。

## 实验验证

论文进行了全面系统的实验评估，涵盖 **215 个音频及音视频理解、推理与交互子任务和基准测试**。实验分为两大类别：**多模态理解（X→Text）**与**语音生成（X→Speech）**，在 Qwen3.5-Omni-Flash 和 Qwen3.5-Omni-Plus 两个模型变体上展开。

## 1. 理解能力评估（X→Text）

### 1.1 文本理解（Text→Text）
评估模型在纯文本任务上的能力保持情况，与 Qwen3.5-Plus-NoThinking 等基线对比：
- **通用知识**：MMLU-Pro、MMLU-Redux、SuperGPQA、C-Eval
- **指令遵循**：IFEval、IFBench
- **长上下文**：AA-LCR、LongBench v2（最长 256k 上下文）
- **STEM 推理**：GPQA
- **代码与数学推理**：LiveCodeBench v6、HMMT Nov 25、IMOAnswerBench
- **智能体能力**：BFCL-V4、TAU2Bench

**关键结果**：Qwen3.5-Omni-Plus 在文本能力上与同规模文本模型 Qwen3.5-Plus-NoThinking 相当（如 MMLU-Pro 85.9 vs 86.8），验证了全模态训练未造成文本能力退化。

### 1.2 音频理解（Audio→Text）
与 Gemini-3.1 Pro 等 SOTA 模型对比，涵盖四大领域：
- **音频理解**：MMAU、MMAR、MMSU、RUL-MuchoMusic、SongFormBench（音乐结构分析）
- **端到端语音对话**：VoiceBench、URO-Bench-pro、SpeechRole、WildSpeech-Bench
- **语音翻译（S2TT）**：Fleurs 数据集上 59 种语言到英语/中文的翻译（BLEU 指标）
- **语音识别（ASR）**：Fleurs、Common Voice、LibriSpeech、WenetSpeech、KeSpeech、Opencpop-test、MIR-1K（词错误率 WER 指标）

**关键结果**：Qwen3.5-Omni-Plus 在 MMAU（82.2）、VoiceBench（93.1）和 ASR（Fleurs WER 6.55%）上显著超越 Gemini-3.1 Pro。

### 1.3 视觉理解（Vision→Text）
验证视觉能力未降级，并与文本模型对比：
- **STEM 与数学推理**：MMMU、MMMU-Pro、MathVision、MathVista、DynaMath、ZEROBench
- **通用视觉问答**：RealWorldQA、MMStar、SimpleVQA
- **文档与 OCR**：CharXiv、CC-OCR、AI2D、MMLongBench-Doc、OCRBench
- **空间智能**：ERQA、CountBench、RefCOCO、ODInW13、EmbSpatialBench
- **视频理解**：Video-MME、MLVU、MVBench、LVBench、MMVU、MME-VideoOCR
- **医学 VQA**：SLAKE、PMC-VQA、MedXpertQA-MM

**关键结果**：在视频理解任务（如 Video-MME 81.9、MLVU 86.8）上，Qwen3.5-Omni-Plus 不仅匹配文本模型，还展现出更强的动态视觉感知能力。

### 1.4 音视频联合理解（AudioVisual Video→Text）
评估跨模态融合与交互能力：
- **文本查询理解**：DailyOmni、WorldSense、AVUT、AV-SpeakerBench、VideoMME（带音频）
- **音频查询交互**：Qualcomm IVD（真实场景音视频对话基准）
- **结构化字幕生成**：Omni-Cloze（细粒度音视频描述）
- **工具使用**：OmniGAIA（原生全模态智能体能力）

**关键结果**：在 DailyOmni（84.6）和 Qualcomm IVD（68.5）上取得 SOTA，OmniGAIA 达到 57.2% 的工具使用准确率。

## 2. 语音生成评估（X→Speech）

### 2.1 零样本语音生成（Zero-Shot TTS）
在 SEED-TTS 基准上评估内容一致性（WER）：
- **对比系统**：Seed-TTS、MaskGCT、E2 TTS、F5-TTS、CosyVoice 2/3、MiniMax-Speech、MiMo-Audio-7B 及前代 Qwen-Omni 模型
- **指标**：词错误率（WER，越低越好）

**关键结果**：Qwen3.5-Omni-Plus 在 test-en 上达到 WER 1.26，超越多数专用 TTS 系统。

### 2.2 多语言语音生成
评估 29 种语言的零样本语音克隆能力：
- **数据集**：TTS 多语言测试集、内部多语言测试集（基于 FLEURS）
- **指标**：词错误率（WER，内容一致性）与余弦相似度（SIM，说话人相似性）
- **对比**：MiniMax-Speech、ElevenLabs

**关键结果**：在 29 种语言中的 22 种上取得最低 WER，并在多数语言上获得最高说话人相似度得分。

### 2.3 跨语言语音生成
评估源语言说话人特征迁移到目标语言的能力：
- **数据集**：Cross-Lingual 基准（12 个语言对，如中→英、日→中、英→日等）
- **指标**：混合错误率（WER 用于英语，CER 用于其他语言）
- **对比**：CosyVoice 2/3、Qwen3-Omni-30B-A3B

**关键结果**：在 12 个方向中的 10 个上取得 SOTA，特别是在 zh-to-ko 上将错误率从 14.4 降至 4.03（相对降低 72%）。

### 2.4 定制语音生成（Custom-Voice）
评估基于用户样本的零样本语音克隆稳定性：
- **数据集**：TTS 多语言测试集（29 种语言）
- **对比**：ElevenLabs Multilingual v2、Gemini-2.5 Pro-Preview-TTS、GPT-Audio-2025-08-28、MiniMax-Speech-2.8-HD

**关键结果**：在 10 种语言上取得最佳 WER，在日语（3.306）和韩语（1.309）等复杂语言上展现跨语言迁移优势。

## 3. 附录中的详细多语言分析

论文附录提供了更细粒度的多语言评估：

### 3.1 多语言语音识别（ASR）
在 FLEURS 测试集（60 种语言）上详细对比：
- **对比模型**：Gemini-3.1 Pro、GPT-4o-Transcribe、Gemini-3-Flash
- **关键发现**：Qwen3.5-Omni-Plus 平均 WER 6.6%，显著优于 Gemini-3.1 Pro（7.3%）和 GPT-4o-Transcribe（10.4%），在粤语（2.2% vs 6.3%）、泰语、越南语等声调语言和低资源语言上优势尤为明显。

### 3.2 多语言翻译
在 FLEURS 测试集上评估四个翻译方向（en2xx、zh2xx、xx2en、xx2zh）：
- **关键发现**：Qwen3.5-Omni-Plus 在 en2xx（平均 BLEU 33.8）和 zh2xx（平均 BLEU 21.4）上超越 Gemini-3.1 Pro，特别是在粤语（+15.6 BLEU）、韩语和日语等亚洲语言对上表现突出。

## 4. 延迟与效率基准

除功能评估外，论文还报告了关键的系统性能指标：
- **首包延迟（First-Packet Latency）**：Flash 版本音频输入 235ms，视频输入 426ms；Plus 版本音频 435ms，视频 651ms
- **并发性能**：在 1/4/8 并发级别下测试 TTFT（首 token 时间）、TTFC（首 chunk 时间）、TPOP（每输出 token 时间）和 TPS（每秒 token 数），验证了 MoE 架构在高并发下的稳定性
- **实时因子（RTF）**：生成 RTF 低至 0.178（Flash，1 并发），确保流式生成的实时性

这些实验全面验证了 Qwen3.5-Omni 在保持文本/视觉能力不降级的前提下，在音频理解、音视频联合推理、实时交互和语音生成方面实现了 SOTA 性能。

## 未来工作

基于论文的技术架构与实验结果，以下方向值得进一步探索：

## 1. 超长上下文的高效建模与推理优化

- **亚线性复杂度注意力机制**：当前 256k 上下文依赖 Hybrid MoE 与 Gated Delta Net 降低 I/O 开销，但随序列长度线性增长的计算复杂度仍是瓶颈。可探索线性注意力（Linear Attention）或状态空间模型（Mamba）与 MoE 的结合，突破长视频（>400 秒）与超长音频（>10 小时）的实时处理限制。
- **分层时间编码**：显式时间戳虽改善了长程时序建模，但对于电影级长视频（数小时）的宏观-微观时间结构（场景-镜头-帧）的分层感知仍可深化，例如引入可学习的时间抽象层级。

## 2. 具身智能与物理世界交互的深度整合

- **视觉-语言-动作（VLA）统一**：当前模型支持 Audio-Visual Vibe Coding（基于音视频指令生成代码），但尚未原生集成物理动作控制（如机器人控制信号）。未来可探索将动作模态（action tokens）纳入统一框架，实现从感知到物理执行的端到端闭环。
- **实时环境反馈机制**：现有交互依赖用户输入，可引入环境传感器流（触觉、深度、IMU）作为持续输入，构建真正的自主智能体（autonomous agent）。

## 3. 低资源语言与方言的鲁棒性提升

- **数据高效学习**：尽管支持 113 种语言，表 13 显示 Tajik、Javanese、Kannada 等低资源语言的 ASR WER 仍显著高于英语/中文（如 Tajik WER 10.0% vs English 3.2%）。可探索跨语言迁移学习的元学习（meta-learning）或合成数据增强策略。
- **方言连续性建模**：当前将 39 种汉语方言作为独立类别，但方言间存在连续谱（continuum），可探索基于地理或音系距离的连续表征空间，而非离散分类。

## 4. 流式生成的稳定性与可控性精细化

- **细粒度韵律控制**：ARIA 解决了文本-语音对齐的稳定性，但情感、风格、语调的细粒度控制（如悲伤程度 0-1 的连续调节）仍依赖提示工程。可引入可学习的控制向量（control vectors）或条件批量归一化（conditional BN）。
- **流式幻觉抑制**：长音频生成中的累积错误（error propagation）机制尚未充分研究，需开发针对流式语音的实时事实核查或自回归纠错机制。

## 5. 多模态幻觉（Hallucination）的评估与缓解

- **音视频错位检测**：现有基准主要关注理解准确率，但缺乏对音视频时序错位（audio-visual misalignment）幻觉的系统评估（如视觉中人物未张嘴但生成语音）。需构建专门的错位检测基准。
- **说话人身份一致性**：在长对话中保持零样本克隆说话人音色的一致性（timbre consistency）仍具挑战，可探索说话人向量（speaker embeddings）的动态更新机制。

## 6. 安全与隐私增强

- **语音生物特征脱敏**：零样本语音克隆能力带来深度伪造风险。需研究在生成过程中自动添加不可听水印（imperceptible watermarking）或说话人特征匿名化技术。
- **对抗鲁棒性**：针对音视频输入的对抗攻击（如微小扰动导致错误转录）防御机制尚未在论文中探讨，需开发多模态对抗训练策略。

## 7. 边缘计算与模型压缩

- **端侧部署**：当前模型规模达数百亿参数，Flash 版本虽优化了延迟，但移动端部署仍需激进压缩（pruning、quantization、蒸馏）。可探索特定于多模态的量化感知训练（QAT），保持音频-视觉表征质量。
- **流式计算图优化**：MTP 模块与 Code2Wav 的流水线并行仍有优化空间，可探索基于 Lookahead 解码或 Speculative Decoding 的进一步加速。

## 8. 交互范式的扩展

- **多智能体多模态对话**：当前支持人机交互，但扩展至多智能体（multi-agent）场景下的多模态协商（negotiation）与协作（collaboration）仍待研究。
- **个性化记忆机制**：长上下文支持 10 小时音频，但跨会话的永久记忆（permanent memory）与知识更新机制（避免灾难性遗忘）需结合外部记忆库（如 vector DB）与参数高效微调（PEFT）。

## 9. 跨模态因果推理

- **物理常识推理**：现有音视频理解侧重于描述性任务，对于物理因果（如"玻璃杯掉落-破碎"的力声学关联）的深层推理能力可进一步加强，需构建物理感知的预训练目标。

## 总结

这篇技术报告介绍了 **Qwen3.5-Omni**，一个支持文本、图像、音频和音视频理解与生成的原生全模态大语言模型。核心内容总结如下：

## 1. 核心架构与创新
- **Thinker–Talker 架构**：Thinker 负责多模态感知与文本生成，Talker 专司流式语音合成，二者均采用 **Hybrid Attention MoE** 设计，支持高达 **256k 上下文长度**（可处理 10 小时音频或 400 秒 720P 视频）。
- **ARIA 技术**：提出自适应速率交错对齐（Adaptive Rate Interleave Alignment），动态对齐文本与语音 token，解决流式语音生成中的跳词、发音错误和韵律不自然问题。
- **多码本语音表示**：采用 RVQ 编解码器与多 token 预测（MTP）模块，实现单帧即时合成与细粒度声学控制。

## 2. 关键能力突破
- **可控音视频字幕**：生成带精确时间戳和自动场景分割的剧本级结构化描述。
- **全面实时交互**：支持语义打断、零样本语音克隆、跨语言语音生成（29 种语言），以及音量、语速、情感的端到端控制。
- **原生智能体行为**：具备自主 WebSearch、FunctionCall 和 Audio-Visual Vibe Coding（直接基于音视频指令生成可执行代码）能力。

## 3. 训练策略
- **三阶段预训练**：编码器对齐 → 通用多模态学习（4T tokens）→ 长上下文优化（262k）。
- **后训练优化**：Thinker 通过专家蒸馏、在线策略蒸馏和交互对齐强化学习提升跨模态一致性与对话质量；Talker 采用 DPO/GSPO 强化学习优化语音自然度。

## 4. 实验性能
- **SOTA 表现**：在 215 个音频及音视频基准测试中，Qwen3.5-Omni-Plus 超越 Gemini 3.1 Pro 的关键音频任务，并在音视频综合理解上与之匹敌。
- **能力保持**：文本与视觉性能与同规模 Qwen3.5 文本模型相当，未出现模态间的性能退化。
- **低延迟**：Flash 版本首包延迟低至 235ms（音频），支持高并发流式推理。

简言之，Qwen3.5-Omni 通过 MoE 架构扩展、ARIA 对齐机制与原生全模态训练，实现了从感知、推理到实时交互与自主行动的统一，代表了全模态智能体模型的重要进展。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
