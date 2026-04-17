# Nemotron 3 Super: Open, Efficient Mixture-of-Experts Hybrid Mamba-Transformer Model for Agentic Reasoning

**arXiv**: [2604.12374](https://arxiv.org/abs/2604.12374) · [PDF](https://arxiv.org/pdf/2604.12374)  
**领域**: Pretraining  
**作者**: NVIDIA, :, Chandiramani, Blakeman, Olaoye, Gupta, Somasamudramath, Khattar 等 547 人  
**综合评分**: 8.31  （novelty: 9.0 · method: 8.5 · evidence: 9.5 · clarity: 7.0）

---

## 摘要

> 本文由NVIDIA团队提出，该团队是全球领先的人工智能计算公司，在GPU加速计算和深度学习领域具有深厚的技术积累和行业影响力。论文介绍了Nemotron 3 Super，一个1200亿参数（激活120亿）的混合Mamba-注意力专家混合模型。该模型在预训练、架构设计和推理加速方面进行了多项创新，并在多个基准测试中取得了具有竞争力的精度，同时显著提升了推理吞吐量。

---

## 详细分析

> **社区热度**: ⭐ 9 (来自 papers.cool)

## 问题定义

这篇论文试图解决大规模语言模型（LLM）在**推理效率、长上下文处理能力与Agentic（智能体）能力**之间的平衡问题，具体体现在以下几个核心维度：

### 1. 推理吞吐量与模型精度的帕累托优化
现有MoE（Mixture-of-Experts）模型虽能通过稀疏激活降低计算成本，但在实际部署中仍面临内存带宽瓶颈（专家权重读取）和通信开销（All-to-All路由）。论文提出**LatentMoE架构**，通过将隐藏维度投影到低维潜在空间（latent space）进行路由和专家计算，在保持相近推理成本的前提下，显著扩展专家数量和激活专家数（Top-$K$），从而提升"单位FLOP精度"（accuracy per FLOP）和"单位参数量精度"（accuracy per parameter）。

### 2. 长上下文推理的内存与计算瓶颈
传统Transformer的自注意力机制具有$O(n^2)$的KV Cache增长问题，严重制约长序列（如1M上下文）的推理效率。论文采用**混合Mamba-2与注意力机制**（Hybrid Mamba-Attention），以线性复杂度的Mamba-2块为主体，仅策略性插入少量全局注意力层作为"锚点"（global anchors），在保持长程依赖建模能力的同时，将生成阶段的内存占用从二次增长降至常数级，显著提升长上下文场景的推理吞吐量。

### 3. Agentic（智能体）推理能力的规模化训练
针对多步骤工具使用、软件工程（SWE-Bench）、终端操作（Terminal Use）等长程交互任务，论文解决了以下挑战：
- **长程轨迹训练的不稳定性**：通过扩展RL环境规模（21个环境类型）和改进异步RL基础设施，支持长达64K tokens的生成长度和复杂的交互轨迹；
- **计算效率与精度的权衡**：提出**PivotRL**方法，在Assistant-turn级别进行强化学习，复用SFT专家轨迹中的关键决策点（pivots），避免端到端RL的高成本，同时防止SFT的分布外（OOD）性能退化；
- **低 effort 推理控制**：引入可配置的推理预算模式（reasoning-off/regular/low-effort），通过RL优化短路径推理能力，满足不同延迟需求。

### 4. 低精度预训练与量化的稳定性
论文探索了在**NVFP4精度**下进行大规模（25T tokens）预训练的可行性，解决了低精度训练中的梯度下溢（underflow）和零值梯度元素增长问题。同时，针对Mamba状态缓存（SSM cache）的递归量化误差积累问题，提出**FP16结合随机舍入（Stochastic Rounding）**的缓存策略，避免量化误差在递归步骤中的相干累积，确保W4A4/W8A8量化下的生成稳定性。

### 5. 原生推测解码（Speculative Decoding）支持
通过**共享权重的多Token预测（MTP）**目标，论文解决了标准MTP在自回归草拟（autoregressive drafting）中的训练-推理分布偏移问题。共享权重设计使模型在推理时可递归使用同一预测头生成长序列草稿，显著提高接受率（acceptance rate），在无需外部草稿模型的情况下实现解码加速。

综上，该论文的核心贡献是构建了一个**在120B总参数/12B激活参数规模下，兼顾高精度Agentic推理、百万级长上下文处理与高效低精度推理**的开源模型体系，在保持与GPT-OSS-120B、Qwen3.5-122B等模型可比精度的同时，实现最高达7.5倍的推理吞吐量提升。

## 相关工作

根据论文内容，相关研究可按技术主题分类如下：

### 1. Mixture-of-Experts (MoE) 架构与稀疏化
- **DeepSeekMoE** (Dai et al., 2024)：提出通过细粒度专家分割与共享专家隔离提升专家专业化程度，为MoE设计提供基础。
- **GShard** (Lepikhin et al., 2020)：最早实现条件计算与自动分片的大规模MoE系统，奠定了分布式MoE训练的基础。
- **DeepSeek-V3** (DeepSeek-AI, 2025c)：采用辅助损失自由的负载均衡策略（auxiliary-loss-free load balancing）与sigmoid路由，本文的RL基础设施与路由设计借鉴了该工作。
- **LatentMoE** (Elango et al., 2026)：本文核心架构的基础，提出在低维潜在空间进行专家计算与路由，以优化"单位字节精度"（accuracy per byte）。

### 2. 混合状态空间与注意力架构
- **Mamba-2 / State Space Duality (SSD)** (Dao & Gu, 2024)：提出结构化状态空间对偶性算法，使状态空间模型（SSM）与注意力机制在理论层面统一，为本文的Hybrid Mamba-Attention设计提供算法基础。
- **Nemotron 3 Nano** (NVIDIA, 2025a; 2025c)：前身模型，首次在Nemotron系列中实现Mamba-2与Transformer的混合架构，本文在此基础上扩展至MoE稀疏化场景。
- **Nemotron-H** (NVIDIA, 2025b)：探索混合架构的长上下文能力，为本文的1M上下文扩展提供技术验证。

### 3. 多Token预测与推测解码
- **Multi-Token Prediction (MTP)** (Gloeckle et al., 2024)：证明多token预测目标可提升模型质量与推理速度，是本文MTP层的理论基础。
- **DeepSeek-V3 MTP** (DeepSeek-AI, 2025c)：独立实现的MTP架构，本文在共享权重设计与推测解码稳定性上与其形成对比。
- **SPEED-Bench** (Abramovich et al., 2026)：本文采用的推测解码评测基准，用于评估MTP接受率与不同模型的草拟质量。

### 4. 低精度训练与量化
- **NVFP4 Pretraining** (NVIDIA, 2025d)：本文低精度预训练的技术基础，提出E2M1格式与二维块缩放（2D block scaling）方案。
- **GPTQ** (Frantar et al., 2023)：后训练量化（PTQ）的代表性方法，本文在FP4量化实验中与该方法进行对比。
- **LLM-MQ** (Li et al., 2023c)：基于Optimal Brain Surgeon的二阶近似混合精度量化，本文的AutoQuantize算法扩展了该工作至权重-激活联合量化场景。
- **FP4 for Inference** (NVIDIA, 2025)：Blackwell架构原生支持的FP4推理格式，本文基于此实现W4A4部署。

### 5. 长上下文建模与评估
- **RULER** (Hsieh et al., 2024)：用于评估长上下文"真实"有效长度的基准测试，本文采用该基准验证1M上下文能力。
- **连续预训练（CPT）与课程学习**：论文中提及的两阶段数据混合策略（Feng et al., 2024）与长上下文扩展方法，与现有长上下文扩展技术（如位置编码外推、YaRN等）形成互补。

### 6. Agentic能力、工具使用与软件工程
- **SWE-Bench** (Jimenez et al., 2023)：软件工程智能体的标准评测基准，本文在此基础上进行多语言扩展与强化学习训练。
- **SWE-Gym** (Pan et al., 2025) 与 **R2E-Gym** (Jain et al., 2025)：提供容器化执行环境与可验证奖励的软件开发训练环境，本文RL阶段直接使用。
- **Tau-Bench** (Barres et al., 2025)：面向对话式工具使用的双控制环境评测，用于评估本文的Agentic工具调用能力。
- **Terminal-Bench** (Merrill et al., 2026; Pi et al., 2026)：命令行界面智能体评测基准，本文Terminal Use数据集构建与评测遵循该工作。
- **PivotRL** (Yi et al., 2026)：本文采用的Assistant-turn级RL方法，通过复用SFT轨迹中的关键决策点（pivots）解决长程Agentic训练的效率与稳定性问题。
- **Deliberative Alignment** (Guan et al., 2025)：本文安全对齐阶段采用的推理-响应分离生成框架。

### 7. 强化学习基础设施与算法
- **GRPO (Group Relative Policy Optimization)** (Shao et al., 2024)：本文RLVR阶段的基础算法，用于多环境可验证奖励训练。
- **NeMo Gym** (NVIDIA, 2025a)：本文开发的开放源码RL环境框架，支持大规模异步训练与多环境协调。
- **vLLM** (Kwon et al., 2023)：本文RL rollout与推理部署采用的内存高效推理引擎，支持PagedAttention与连续批处理。
- **Checkpoint Merging / Weight Averaging** (Wortsman et al., 2022; Tian et al., 2025)：本文在预训练阶段采用的模型融合技术，用于稳定学习率稳定期的质量评估。

### 8. 合成数据生成与数据工程
- **Nemotron-CC** (Su et al., 2025)：本文预训练采用的Common Crawl精炼数据集，基于质量分层与合成数据增强。
- **MIND (Math Informed synthetic Dialogues)** (Akter et al., 2024)：数学推理合成数据生成方法，本文数学数据集构建参考其流程。
- **GenSelect** (Toshniwal et al., 2025)：本文财务推理数据集采用的Best-of-N生成与选择策略。
- **NeMo Data Designer**：本文大量SFT与合成数据生成使用的流水线框架，支持基于知识图谱的查询生成（如搜索数据集）与多智能体模拟（如工具使用数据集）。

## 解决方案

论文通过**架构创新、训练策略优化、后训练对齐与工程化部署**四个层面的系统性方法，解决了大规模语言模型在效率、长上下文与Agentic能力之间的平衡问题。具体解决方案如下：

---

### 1. 架构层面：LatentMoE 与混合状态空间设计

#### 1.1 LatentMoE：硬件感知的稀疏专家架构
针对传统MoE的内存带宽与通信瓶颈，论文提出**LatentMoE**架构（§2.1.1）。该架构将输入token从隐藏维度 $d$ 投影到低维潜在空间 $\ell$（latent dimension），在该空间内完成路由与专家计算：

- **降维投影**：通过可学习的下投影矩阵 $W_{\downarrow} \in \mathbb{R}^{\ell \times d}$ 压缩表示，专家计算在 $\mathbb{R}^{\ell}$ 空间完成，再通过上投影矩阵 $W_{\uparrow} \in \mathbb{R}^{d \times \ell}$ 恢复维度。
- **专家规模扩展**：利用维度降低带来的参数节省（减少因子 $d/\ell$），将总专家数从 $N$ 扩展至 $N' = N \cdot d/\ell$，同时将每token激活专家数从 $K$ 提升至 $K' = K \cdot d/\ell$。
- **非路由计算保持全精度**：路由门控、共享专家及非专家层保持原始维度 $d$，确保关键路径的建模能力。

该设计使模型在相近的推理成本下，通过增加专家组合空间提升精度，实现了更高的**单位参数量精度**（accuracy per parameter）与**单位FLOP精度**（accuracy per FLOP）。

#### 1.2 混合Mamba-Attention架构
为解决长上下文下KV Cache的二次增长问题，论文采用**周期性交错的混合架构**（§2.1.3）：
- **主体为Mamba-2块**：在88层网络中，主体使用具有线性复杂度 $O(n)$ 的Mamba-2状态空间模型，其生成阶段状态缓存大小恒定，与序列长度无关。
- **全局注意力锚点**：策略性插入自注意力层作为"全局锚点"（global anchors），启用分组查询注意力（GQA，32查询头/2 KV头），在保持长程依赖建模能力的同时，将内存开销降至Mamba的常数级别与注意力的线性级别混合。
- **长上下文支持**：该架构原生支持最高**1M tokens**的上下文长度（§2.6）。

#### 1.3 共享权重多Token预测（MTP）
为提升推理速度，论文引入**共享权重的MTP层**（§2.1.2）：
- **训练目标**：优化模型在每个位置预测多个未来token的能力，损失函数为：
  $$\mathcal{L}_{\text{MTP}} = \sum_{t} \sum_{i=1}^{D} \ell_{t+i}$$
  其中 $D$ 为预测深度，$\ell$ 为负对数似然。
- **共享头设计**：多个预测头共享参数，在推理时可递归使用同一头进行自回归草拟（autoregressive drafting），缓解训练-推理分布偏移，支持更长的推测序列（draft length up to 7）。
- **原生推测解码**：MTP头作为内部草稿模型，通过验证-接受机制减少解码步数，在Blackwell硬件上实现吞吐量-延迟帕累托前沿的显著左移（Figure 5）。

---

### 2. 预训练策略：低精度训练与数据工程

#### 2.1 NVFP4低精度预训练
论文首次实现在**NVFP4格式**（E2M1元素格式，16元素微块）下稳定训练120B参数模型至25T tokens（§2.2）：
- **混合精度策略**：除最后15%网络层、潜在投影、MTP层、QKV及注意力投影保持BF16外，所有线性层使用NVFP4进行前向/反向/权重梯度GEMM计算。
- **随机Hadamard变换（RHT）**：应用于wgrad输入，结合随机舍入（stochastic rounding）缓解梯度下溢。
- **训练稳定性**：针对专家层出现的通道幅度模式（channel magnitude patterns）导致的零值梯度增长，通过监控与验证确认其不影响下游精度（Figure 6-9）。

#### 2.2 两阶段课程学习与数据混合
- **Phase 1（20T tokens，80%）**：强调数据多样性，涵盖网页爬取（Nemotron-CC）、数学、代码、多语言等16个类别，使用不同质量分层的混合策略（Figure 10a）。
- **Phase 2（5T tokens，20%）**：聚焦高质量数据（如Wikipedia、finepdfs-high）与特定能力增强（合成代码概念、算法、经济学、形式逻辑、多项选择题），提升基准精度（Figure 10b）。

#### 2.3 长上下文连续预训练（CPT）
在25T tokens后增加**长上下文阶段**（§2.6）：
- 使用1M上下文长度与恒定学习率 $4.5 \times 10^{-6}$，结合64路上下文并行与64路专家并行。
- 数据混合包含20%文档QA数据集与80%降采样Phase 2数据，总计34B tokens。
- 后续增加1M与4K序列交替训练阶段（17B tokens），缓解对数学基准的轻微负面影响。

#### 2.4 检查点融合（Checkpoint Merging）
在WSD学习率稳定期，采用**滑动窗口加权平均**（§2.5）：
- 使用minus-sqrt衰减系数对最近125B-500B tokens的检查点进行融合，平均提升基准精度2-4点，避免专门的学习率退火运行，节省约4T tokens计算量（16%总预算）。

---

### 3. 后训练对齐：多阶段强化学习与Agentic优化

#### 3.1 两阶段监督微调（SFT）
- **Stage 1（Token级平均）**：使用256K序列打包，优化全局平均损失：
  $$\mathcal{L}_{\text{tok}} = \frac{\sum_{c \in \mathcal{B}} \sum_{t \in \mathcal{O}_c} \ell_t}{\sum_{c \in \mathcal{B}} |\mathcal{O}_c|}$$
  强化推理行为学习。
- **Stage 2（样本级平均）**：切换至512K序列打包，采用每对话归一化：
  $$\mathcal{L}_{\text{samp}} = \frac{1}{|\mathcal{B}|} \sum_{c \in \mathcal{B}} \left( \frac{1}{|\mathcal{O}_c|} \sum_{t \in \mathcal{O}_c} \ell_t \right)$$
  防止长输出主导损失，恢复长输入-短输出性能。

#### 3.2 多环境强化学习（RL）
论文构建**三阶段RL流水线**（§3.2）：

- **Stage 1: RLVR（可验证奖励RL）**  
  在21个环境、37个数据集上同步训练，涵盖数学（含形式化证明验证）、代码、STEM、指令遵循、安全（越狱鲁棒性）、长上下文与Agentic工具使用。采用**异步GRPO**（§3.2.4），解耦训练与推理，支持最大64K生成长度。引入**低努力推理模式（low-effort reasoning）**（§3.2.1），通过调整奖励函数（正确性与token数加权）优化短路径推理。

- **Stage 2: SWE-RL（软件工程RL）**  
  隔离训练以处理长程、长上下文轨迹（OpenHands/OpenCode/Codex多工具格式）。每个rollout启动Apptainer容器执行真实代码库交互，通过单元测试验证获得二进制奖励。

- **Stage 3: RLHF（人类反馈RL）**  
  使用基于原则遵循的GenRM（Generative Reward Model，Qwen3-235B-A22B初始化）进行监督，结合HelpSteer 3与lmarena-140k数据集，优化指令遵循与交互质量。

- **Stage 4: MTP愈合**  
  冻结主干网络，单独训练MTP头以恢复多token预测精度。

#### 3.3 PivotRL：高效Agentic训练
针对长程交互任务（终端使用、搜索、对话式工具使用），采用**PivotRL**方法（§3.2.4）：
- 复用SFT专家轨迹，识别策略不确定性高的关键决策点（pivots）。
- 在pivot处应用领域适配的奖励函数，匹配策略动作与专家动作（而非精确复制），实现高效离线-在线混合训练，避免端到端RL的高成本与SFT的OOD退化。

---

### 4. 量化与推理优化

#### 4.1 后训练量化（PTQ）
- **FP8 (W8A8)**：对MoE GEMM（路由与共享专家）及Mamba线性层进行量化，KV Cache保持FP8，Mamba状态缓存（SSM cache）量化为FP16。
- **NVFP4 (W4A4)**：采用**MSE最小化的逐块权重缩放**与**动态最大值激活缩放**混合策略，结合**AutoQuantize**（基于二阶泰勒近似的神经架构搜索）在4.75 bits有效精度预算下自动分配层精度（Table 7）。

#### 4.2 Mamba状态缓存量化
针对递归状态量化误差的时序累积问题（公式3）：
$$h_{q,t} = h_t + \sum_{i=0}^{t} \left( \prod_{j=i+1}^{t} A_j \right) e_i$$
论文采用**FP16结合随机舍入（Stochastic Rounding, Philox<5>）**（§4.3），替代传统的最近偶数舍入（RTNE），将系统性偏差转化为零均值噪声，避免 verbosity 激增与精度下降（Table 9）。

---

### 5. 基础设施创新

- **NeMo Gym / NeMo RL**：基于Ray的异步RL基础设施，支持1000+ GPU规模，实现in-flight权重更新、一步离线策略训练与多环境协调（§3.2.5）。
- **SWE-RL执行环境**：基于Apptainer的容器化沙箱，集成OpenHands代理循环、内存监控看门狗与命令黑名单，支持并发代码库交互与测试验证。

通过上述架构-训练-对齐-部署的全栈优化，Nemotron 3 Super在保持与GPT-OSS-120B、Qwen3.5-122B可比精度的同时，实现了**最高7.5倍的推理吞吐量提升**与**1M上下文长度的原生支持**。

## 实验验证

论文中的实验涵盖**架构验证、预训练稳定性、基础模型评估、后训练对齐、量化优化及端到端推理性能**六个维度，具体如下：

---

### 1. 架构验证实验

#### 1.1 多Token预测（MTP）质量评估
在**SPEED-Bench**（推测解码专用基准）上评估MTP层的草稿接受能力：
- **设置**：固定草稿长度7，对比Nemotron 3 Super、DeepSeek-R1与Qwen3-Next。
- **指标**：平均接受长度（average acceptance length）与逐位置接受率（acceptance rate by draft index）。
- **结果**：Nemotron 3 Super达到**平均3.45 tokens/步**的最高接受长度，且在草稿索引4-7的长程位置显著优于DeepSeek-R1（Figure 4, Table 2）。

#### 1.2 推测解码吞吐量验证
在Blackwell B300 GPU上测量MTP对推理效率的提升：
- **设置**：对比MTP关闭与草稿深度 $D=1, 3$ 的NVFP4检查点（TRT-LLM, TP=1）。
- **指标**：总吞吐量（total tokens/s/GPU）与中位用户延迟（median user latency）。
- **结果**：$D=3$ 显著将吞吐量-延迟帕累托前沿向左上方推移（Figure 5）。

---

### 2. 预训练稳定性与优化实验

#### 2.1 NVFP4低精度训练稳定性
验证120B参数模型在NVFP4格式下训练25T tokens的稳定性：
- **观察指标**：专家层权重梯度中零值元素比例、通道幅度模式（channel magnitude patterns）演化。
- **对比实验**：Nemotron 3 Nano架构上对比NVFP4与BF16训练至1T tokens，NVFP4产生约3倍零值梯度，但下游精度未受影响；中途切换回BF16可使零值梯度恢复基线（Figure 6-7）。
- **MXFP8修复尝试**：在19T tokens时将所有张量提升至MXFP8精度，虽改善损失轨迹，但下游任务精度无持续提升（Figure 9）。

#### 2.2 检查点融合（Checkpoint Merging）消融
在WSD学习率稳定期评估滑动窗口融合策略：
- **设置**：测试125B、250B、500B tokens三种融合窗口，使用minus-sqrt衰减系数。
- **指标**：12项基准（MMLU-Pro、GSM8K、HumanEval等）的无加权平均精度。
- **结果**：融合检查点比原始检查点稳定提升**2-4点**平均精度；在5T tokens的LR退火阶段，融合与训练检查点差距收窄（Figure 11, Appendix Figure 17）。

#### 2.3 长上下文连续预训练（CPT）验证
- **设置**：基础模型（8K上下文）→ 1M上下文CPT（34B tokens）→ 1M/4K交替训练（17B tokens）。
- **评估**：RULER基准在64K-1M长度上的 needle-in-haystack 精度。
- **结果**：RULER 1M达到**71.00%**精度（Table 4），且交替训练缓解了数学基准的轻微退化。

---

### 3. 基础模型基准测试（Table 4）

与Ling-flash-Base-2.0和GLM-4.5-Air-Base对比，涵盖：
- **通用知识**：MMLU（86.01%）、MMLU-Pro（75.65%）、GPQA-Diamond（60.00%）。
- **数学推理**：GSM8K（90.67%）、MATH-500（84.84%）、MATH Level 5（70.00%）、AIME 2024 pass@32（53.33%）。
- **代码生成**：HumanEval（79.40%）、MBPP-Sanitized（78.38%）。
- **长上下文**：RULER 64K（92.26%）、128K（88.26%）、256K（84.56%）、512K（82.49%）、1M（71.00%）。

---

### 4. 后训练对齐实验

#### 4.1 监督微调（SFT）两阶段对比
- **Stage 1（Token级平均）**：使用256K序列打包，优化全局平均损失 $\mathcal{L}_{\text{tok}}$，强化推理行为。
- **Stage 2（样本级平均）**：切换至512K序列打包，优化 $\mathcal{L}_{\text{samp}}$，恢复长输入-短输出性能（§3.1）。

#### 4.2 多环境强化学习（RLVR）规模验证
- **环境规模**：21种环境类型、37个数据集（数学、代码、STEM、安全、工具使用等）。
- **算法**：异步GRPO，批次大小4096，最大生成长度从49K逐步增至64K tokens。
- **低 effort 推理**：在2%→1%的RL提示上应用基于token数的奖励调整，优化短路径推理（§3.2.1）。

#### 4.3 PivotRL Agentic训练效率
在Agentic编程、搜索、终端使用、对话式工具使用任务上验证PivotRL：
- **方法**：复用SFT专家轨迹，在关键决策点（pivots）进行Assistant-turn级RL。
- **结果**：相比端到端RL显著降低计算成本，相比纯SFT避免OOD性能退化（§3.2.4）。

---

### 5. 量化与推理优化实验

#### 5.1 后训练量化（PTQ）精度保持（Table 8）
对比BF16基线与两种量化格式：
- **FP8 (W8A8)**：MMLU-Pro 83.63（vs 83.73 BF16），HMMT 94.38（vs 94.73）。
- **NVFP4 (W4A4)**：MMLU-Pro 83.33，平均达到BF16的**99.8%中位精度**。

#### 5.2 PTQ算法消融（Appendix Table 10）
在MMLU-Pro、GPQA、LiveCodeBench、AA-LCR上对比：
- 默认NVFP4（max-based scaling）：MMLU-Pro 82.99。
- **MSE最小化逐块权重缩放**：MMLU-Pro **83.31**（最佳）。
- GPTQ权重量化：MMLU-Pro 83.11。
- 输出MSE最小化：MMLU-Pro 83.05。

#### 5.3 Mamba SSM缓存量化策略（Table 9）
在LiveCodeBench与SciCode上测试不同缓存精度：
- **FP32基线**：精度72.91/40.90，verbosity 21769/3680 tokens。
- **FP16（RTNE）**：精度73.24/42.01，verbosity激增36.95%/2.19%。
- **FP16+随机舍入（Philox<5>）**：精度72.00/41.94，verbosity -1.73%/-2.72%，恢复基线水平。
- **W8A8+INT16块缩放**：精度72.22/41.46，verbosity 2.90%/-4.30%。

#### 5.4 AutoQuantize混合精度搜索
使用基于二阶泰勒近似的敏感度估计，在4.75 bits有效预算下：
- 稀疏专家GEMM：全部分配NVFP4。
- 注意力与Mamba投影GEMM：分配FP8或BF16。
- 共享专家GEMM：混合NVFP4/FP8/BF16（Table 7）。

---

### 6. 端到端推理性能评估（Figure 1）

在8K输入/64K输出设置下，使用B200 GPU对比：
- **Nemotron 3 Super (NVFP4)**：相对吞吐量**2.2×**于GPT-OSS-120B (MXFP4)，**7.5×**于Qwen3.5-122B (BF16)。
- **精度**：在IFBench、HMMT、SWE-Bench、HLE、Terminal Bench等Agentic基准上与竞品持平或更优。

### 7. 合成数据消融验证

- **合成MCQ数据**：在Nemotron 3 Nano上增加100B tokens训练，其中1B为合成MMLU数据，MMLU提升0.29点（77.22→77.51），MATH Level 5提升0.5点（§2.3.6）。
- **合成算法代码数据**：在Nemotron 3 Nano最后100B tokens中加入，HumanEval/MBPP/CRUXEval-O提升1-2点（§2.3.3）。

## 未来工作

基于论文内容，以下方向值得进一步探索：

---

### 1. 架构设计与稀疏化
**动态潜在维度与自适应路由**  
论文中LatentMoE采用固定的潜在维度 $\ell$ 进行投影（§2.1.1）。可探索**动态潜在维度**，根据输入token的复杂度自适应调整压缩率，或引入可学习的维度选择机制，在简单token上使用更低维度以进一步节省带宽，复杂token保留更高维度。

**更细粒度的专家特化**  
当前LatentMoE通过增加专家数 $N'$ 和激活数 $K'$ 提升容量。可研究**层次化路由**（hierarchical routing）或**专家内再分割**（intra-expert specialization），在潜在空间内部进一步划分子专家，实现更细粒度的知识分离。

---

### 2. 低精度训练的理论与稳定性
**NVFP4训练中的零值梯度现象**  
论文观察到训练结束时7%的参数出现零值梯度（§2.2），虽经验证不影响下游精度，但其根本原因（低范数通道在NVFP4中更快衰减）仍需深入。可探索：
- **结构化稀疏性诱导**：是否可将此现象转化为显式的结构化剪枝？
- **自适应精度切换**：在检测到梯度下溢风险时动态提升特定层至MXFP8/BF16，而非固定切换策略。

**低精度Mamba状态训练**  
当前Mamba SSM缓存量化仅在推理阶段处理（§4.3）。探索在**预训练阶段即对SSM状态进行低精度建模**（如QAT for SSM states），使模型学会对状态量化噪声鲁棒的表示。

---

### 3. 长上下文与复杂推理的协同优化
**上下文长度与推理深度的帕累托前沿**  
论文发现1M上下文CPT会轻微损害数学基准（§2.6），需通过交替训练缓解。这暗示**长上下文记忆与复杂符号推理**可能存在资源竞争。可研究：
- **认知路由机制**：让模型自主决定是否使用长上下文记忆或进行深度推理（如通过router选择"回忆模式"vs"推理模式"）。
- **分层上下文压缩**：对长历史进行层次化摘要（hierarchical summarization），而非直接存储完整KV或Mamba状态。

**多模态长上下文扩展**  
当前长上下文仅针对文本（1M tokens）。将架构扩展至**图像-文本交错的长序列**（如长视频理解），需重新设计Mamba-Attention的混合模式以处理非因果视觉特征。

---

### 4. Agentic能力的强化学习
**PivotRL的深入理论分析**  
论文提及PivotRL将在后续工作中详细阐述（§3.2.4）。当前可探索：
- **最优Pivot选择策略**：基于信息增益或价值估计动态选择关键决策点，而非静态不确定性阈值。
- **跨任务Pivot迁移**：将在软件工程任务中学到的pivots迁移至终端操作等新领域。

**多智能体协作的RL训练**  
当前RL环境主要关注单智能体与环境的交互（§3.2）。扩展至**多智能体协作场景**（如多人编程、分布式工具调用），需解决信用分配（credit assignment）与异步通信的RL优化问题。

**推理预算的细粒度控制**  
低effort模式（§3.2.1）当前通过token数惩罚实现。可探索**基于难题度预测的动态预算分配**，让模型在简单问题上自动缩短推理链，复杂问题自动扩展，形成"自适应思考深度"。

---

### 5. 量化与推理效率
**SSM缓存的 learnt quantization**  
当前采用FP16+随机舍入（§4.3）缓解递归误差累积。可探索**针对递归结构的专用量化方案**，如：
- **时域感知的误差反馈**（temporal error feedback）：将第 $t$ 步的量化误差显式补偿至第 $t+1$ 步。
- **状态空间分解量化**：对Mamba的 $A, B, C$ 矩阵分别采用不同精度，利用其结构特性（如 $A$ 的对角化）。

**推测解码与MoE的协同优化**  
论文提到MTP在MoE设置下有效（§2.1.2），但未深入探讨**草稿模型与专家路由的交互**。可研究：
- **投机性专家预取**（speculative expert prefetching）：根据MTP草稿提前加载可能激活的专家，减少MoE all-to-all延迟。
- **动态草稿深度**：根据当前激活专家的负载动态调整MTP草稿长度 $D$。

**混合精度自动搜索的扩展**  
AutoQuantize（§4.2）基于二阶近似。可引入**运行时敏感度分析**，在推理过程中根据输入分布动态调整层精度，实现"输入自适应的混合精度"。

---

### 6. 数据合成与评估
**合成数据的 scaling laws**  
论文中合成数据（代码概念、经济学、形式逻辑等，§2.3）规模相对较小（如算法数据仅0.2B tokens），但带来1-2点提升。需建立**合成数据规模与模型性能 scaling law**，确定不同能力（如形式逻辑vs代码）的最优合成数据量。

**Agentic能力的综合评价基准**  
当前Agentic评估分散于SWE-Bench、Terminal Bench等（§3.3）。构建**跨域Agentic能力基准**（如同时涉及代码修改、网页搜索与终端操作的复合任务），评估模型在长程、多工具、多模态环境中的鲁棒性。

**安全性与有用性的长期对齐**  
论文采用两阶段 deliberative alignment（§3.1.1）。需研究在**持续学习（continual learning）**场景下，如何防止安全对齐的灾难性遗忘，同时保持模型对新兴工具（如MCP servers）的适应性。

---

### 7. 系统工程与基础设施
**超大规模异步RL的稳定性**  
论文在1K GPU规模遇到TOCTOU竞争条件与硬件故障（§3.2.5）。可探索：
- **确定性检查点与状态恢复**：实现RL训练的容错机制，避免单点故障导致整个实验重启。
- **异构计算调度**：将生成（高内存带宽需求）与训练（高算力需求）任务调度至不同类型的GPU（如B200 for inference, H100 for training）。

**容器化Agent执行的安全强化**  
当前使用Apptainer与命令黑名单（§3.2.5）。可探索**形式化验证的沙箱**或**eBPF-based系统调用过滤**，在允许Agent执行任意代码的同时，提供更强的安全隔离保证。

## 总结

论文介绍了 **Nemotron 3 Super**，一个拥有1200亿总参数（120B）但仅激活120亿参数（12B）的开放、高效混合架构大语言模型，专为Agentic推理、长上下文处理与高效推理优化而设计。

### 核心架构创新
- **LatentMoE**：一种新型稀疏专家架构，通过将token投影至低维潜在空间（latent space）进行路由与计算，将内存带宽与通信开销降低 $d/\ell$ 倍，同时将专家总数与激活专家数（Top-$K$）同比扩展，实现更高的"单位参数量精度"与"单位FLOP精度"。
- **混合Mamba-Attention**：以线性复杂度的Mamba-2状态空间模型为主体（占88层中的大部分），策略性插入少量全局自注意力层作为"锚点"，在保持1M tokens长上下文建模能力的同时，将生成阶段内存开销从二次降至线性/常数级。
- **共享权重多Token预测（MTP）**：通过共享参数的预测头实现原生推测解码（speculative decoding），支持递归自回归草拟，在SPEED-Bench上达到平均3.45 tokens/步的接受长度，显著提升推理吞吐量。

### 训练与对齐策略
- **NVFP4低精度预训练**：首次在25万亿tokens预训练中全程使用NVFP4格式（E2M1），结合随机Hadamard变换与混合精度策略（关键层保持BF16），验证了大模型低精度训练的稳定性。
- **两阶段课程学习**：Phase 1（20T tokens）强调数据多样性；Phase 2（5T tokens）聚焦高质量数据与合成数据（代码概念、算法、经济学、形式逻辑等），并辅以检查点融合（checkpoint merging）技术稳定评估。
- **长上下文扩展**：通过连续预训练（CPT）将上下文从8K扩展至1M tokens，采用1M/4K交替训练策略缓解对数学能力的负面影响。
- **三阶段后训练**：
  - **SFT**：采用两阶段损失（Token级平均→样本级平均）平衡推理能力与长输入-短输出性能。
  - **RLVR**：在21个环境、37个数据集上异步训练，引入低effort推理模式优化推理长度与精度的权衡。
  - **SWE-RL**：隔离训练软件工程任务，支持多工具格式（OpenHands/OpenCode/Codex）与容器化执行。
  - **RLHF**：基于原则遵循的GenRM进行人类偏好对齐。
  - **PivotRL**：针对长程Agentic任务，在关键决策点（pivots）复用专家轨迹进行高效RL训练，避免端到端RL的高成本与SFT的OOD退化。

### 量化与推理优化
- **混合精度量化**：通过AutoQuantize（基于二阶敏感度估计的NAS方法）生成NVFP4（W4A4）与FP8（W8A8）检查点，在4.75 bits有效预算下达到BF16基线99.8%的中位精度。
- **Mamba状态缓存量化**：提出FP16结合随机舍入（Stochastic Rounding）策略，解决递归状态量化误差的时序累积问题，避免生成长度激增（verbosity explosion）。

### 性能与开源贡献
- **推理效率**：在8K输入/64K输出场景下，NVFP4格式的Nemotron 3 Super在B200 GPU上实现比GPT-OSS-120B（MXFP4）高**2.2倍**、比Qwen3.5-122B（BF16）高**7.5倍**的吞吐量。
- **精度表现**：在MMLU-Pro、HMMT、SWE-Bench、Terminal Bench、RULER（1M上下文）等基准上与上述模型持平或更优。
- **开源发布**：发布预训练、后训练、FP8及NVFP4量化检查点，以及预训练专用数据集（Nemotron-Pretraining-Specialized-v1.1）与后训练数据集（Nemotron-Super-Post-Training-Data）。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
