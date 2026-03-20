# Seeing Beyond 8bits: Subjective and Objective Quality Assessment of HDR-UGC Videos

**arXiv**: [2603.00938](https://arxiv.org/abs/2603.00938) · [PDF](https://arxiv.org/pdf/2603.00938)  
**领域**: Multimodal  
**作者**: Saini, Chen, Birkbeck, Wang, Adsumilli, Bovik  
**综合评分**: 8.43  （novelty: 9.0 · method: 8.0 · evidence: 9.5 · clarity: 8.0）

---

## 摘要

> 本文由德克萨斯大学奥斯汀分校（UT Austin）的Bovik教授团队（图像与视频工程实验室）提出，该团队在图像/视频质量评估领域享有盛誉。论文针对HDR用户生成视频质量评估这一新兴挑战，构建了大规模主观数据集Beyond8Bits，并提出了首个基于多模态大语言模型的HDR-UGC VQA方法HDR-Q，在创新性和实验规模方面表现突出。

---

## 详细分析

> **社区热度**: ⭐ 1 (来自 papers.cool)

## 问题定义

这篇论文针对**高动态范围（HDR）用户生成内容（UGC）视频的主观质量评估**这一核心挑战，具体试图解决以下关键问题：

## 1. HDR-UGC 视频质量评估的数据稀缺性
现有HDR视频质量评估数据集存在明显局限：规模小、多聚焦于专业制作内容或合成失真，缺乏对真实世界中用户生成HDR内容的覆盖。这导致无法充分捕捉非受控拍摄条件下的异构退化（如设备差异、动态范围变化、压缩伪影等）。

## 2. 现有VQA模型对HDR内容的适应性不足
传统视频质量评估（VQA）模型主要针对标准动态范围（SDR）设计，难以处理HDR视频特有的技术特征：
- **更高位深**（10-bit vs 8-bit）和**扩展亮度范围**（PQ/HLG传输曲线）
- **宽色域**（BT.2020）带来的色彩保真度要求
- **HDR特定失真**：近黑区域压碎（near-black crushing）、高光裁剪（highlight clipping）、色带（banding）、曝光闪烁（exposure flicker）等

这些失真在SDR中不明显，但在HDR显示条件下显著影响主观感知质量。

## 3. 多模态大语言模型（MLLM）在HDR-VQA中的固有缺陷
将MLLM直接应用于HDR-UGC质量评估面临三重障碍：
- **视觉编码器偏差**：标准视觉编码器在SDR数据上预训练，无法提取HDR特有的亮度、对比度和色域线索；
- **连续质量回归困难**：在"下一个token预测"范式下，离散分级或回归头方法缺乏细粒度的平均意见分（MOS）校准能力；
- **模态忽视（Modality Neglect）**：缺乏显式约束时，策略模型倾向于依赖文本先验而非HDR视觉输入，导致推理过程与视觉证据脱节。

## 4. 缺乏针对HDR感知特性的优化训练框架
现有强化学习方法（如GRPO）缺乏确保模型**基于HDR线索进行推理**的机制，无法保证模型在生成质量判断时真正利用高动态范围信息而非依赖SDR等效输入。

通过提出**Beyond8Bits数据集**（含$\sim$44K视频、>1.5M人工评分）和**HDR-Q模型**（集成HDR感知视觉编码器与HDR感知策略优化框架HAPO），该论文系统性地解决了上述数据与算法层面的瓶颈。

## 相关工作

该论文的相关研究主要分布于**HDR视频质量评估（HDR-VQA）**与**多模态大语言模型（MLLM）感知质量评估**两大领域，同时涉及**强化学习优化策略**的基础方法。

### 1. HDR-VQA 数据集与模型

**传统SDR-VQA基础**
早期VQA数据集（CVD2014, LIVE-VQA, LIVE-VQC, LSVQ, MDVQA, Maxwell）及对应的传统手工特征方法（如BRISQUE, VBLIINDS）与深度学习方法（FastVQA, FasterVQA, DOVER, CONVIQT, COVER）主要针对SDR内容，因亮度范围与色调映射的根本差异而无法直接迁移至HDR场景。

**HDR专用数据集**
- **早期数据集**：由Azimi等、Pan等、Baroncini等、Rerabek等、Athar等构建的主观数据集，存在规模小或场景受限问题；
- **现代基准**：LIVE-HDR（310段标注视频）、SFV+HDR（2,000片段，300段评分）以及HDRSDBVQA等，为现代HDR算法提供更可靠的评估基准；
- **作者前期工作**：HIDRO-VQA（利用大规模无标签HDR数据预训练）、CHUG（众包HDR-UGC数据集）。

**HDR-VQA算法**
- **全参考方法**：HDR-VQM、HDR-BVQM、PU21，基于亮度感知或感知均匀变换，但依赖参考视频且难以处理多样化HDR失真；
- **无参考方法**：HDR-ChipQA（通过非线性亮度映射扩展ChipQA）、HIDRO-VQA（基于CONTRIQUE架构利用无标签HDR数据学习）。

### 2. MLLM-based 感知质量评估

**基础能力与基准**
- **Q-Bench**：首次系统评估MLLM在低层视觉任务上的表现，揭示其与传统质量评估模型间的显著差距；
- **视频扩展**：Q-Bench-Video、MVQA-68K提供大规模多维标注与文本理由，支持视频感知质量的MLLM训练。

**优化策略与架构**
- **指令微调**：Q-Instruct通过专门指令集提升MLLM的低层视觉能力；
- **描述性推理**：DepictQA与DepictQA-Wild引入基于描述的失真推理范式；
- **比较与排序**：Compare2Score、VisualQuality-R1利用对比学习与排序优化提升人类判断对齐度；
- **可解释性与回归**：Q-Align（离散文本定义层级）、DeQA-Score（分布回归）、Q-Insight（联合退化推理）分别针对可解释性、回归保真度与多退化联合建模。

### 3. 强化学习优化框架

**基础算法**
- **GRPO（Group Relative Policy Optimization）**：通过组内奖励归一化消除传统PPO中价值网络的方差问题，成为现代LLM/MLLM后训练的核心组件；
- **DPO（Direct Preference Optimization）**：作为GRPO的对比基础，用于偏好建模。

**模态对齐与正则化**
- **模态忽视（Modality Neglect）**：Zheng等、Wang等指出MLLM在推理中忽视视觉输入而依赖文本先验的问题；
- **熵正则化与不确定性**：Rafailov等、Zeng等关于不确定性操控与熵崩溃（entropy collapse）的研究，为HAPO中的双熵正则化提供理论支撑；
- **互信息估计**：Ishmael Belghazi等提出的MINE方法，为HAPO中HDR-SDR对比KL的互信息视角提供基础。

### 4. 视觉编码与基础模型

**视觉编码器**
- **SigLIP-2**：作为HDR-Q视觉编码器的基座模型，提供多语言视觉-语言对齐能力与改进的密集特征；
- **Qwen2.5-VL、GLM-4.1V-Thinking、Ovis2.5**：作为MLLM基线参与对比实验，代表当前开源多模态推理模型的技术水平。

**HDR信号处理**
- **色调映射算子**：PQ（SMPTE ST 2084）与HLG（Hybrid Log-Gamma）传输曲线的基础标准；
- **感知均匀编码**：PU21编码，用于将现有SDR质量指标适配至HDR领域。

## 解决方案

该论文通过**数据构建**与**算法创新**双轨并行的方式解决HDR-UGC视频质量评估难题，具体实施方案如下：

---

### 1. 构建大规模HDR-UGC主观数据集（Beyond8Bits）

针对现有HDR数据集规模小、场景局限的问题，论文构建了迄今最大的真实世界HDR-UGC质量数据集：

**数据采集与处理**
- **双源采集**：2,253段来自用户众包（iPhone/Pixel/Galaxy等消费设备），4,608段来自Vimeo Creative Commons授权视频，共6,861段独立源视频；
- **HDR信号保真**：严格验证PQ传输曲线、10-bit HEVC编码、BT.2020色域元数据，剔除重复与静态内容；
- **真实压缩模拟**：按YouTube/Apple HLS规范构建码率阶梯（0.2–5 Mbps），跨分辨率（360p–1080p）转码，生成约44,276段处理片段。

**大规模众包主观实验**
- **HDR显示验证**：通过Amazon Mechanical Turk筛选具备HDR显示能力与兼容浏览器的受试者，实施ITU-R BT.500-14标准；
- **质量控制机制**：嵌入重复视频与黄金标准集（golden-set）检验 intra/inter-subject 一致性，结合网络速度、完成时间、显示位深检测剔除无效数据；
- **MOS聚合**：采用SUREAL（Subjective Reliability）方法，建立最大似然估计模型 $S_{ij} = \psi_j + \Delta_i + \nu_i X$ 以校正受试者偏差与不稳定性，最终获得>1.5M有效评分。

---

### 2. 提出HDR-Q：面向HDR的MLLM质量评估框架

HDR-Q是首个专为HDR-UGC设计的MLLM，由**HDR感知视觉编码器**与**HDR感知策略优化（HAPO）**两大核心模块构成。

#### 2.1 HDR-Aware Vision Encoder

解决标准视觉编码器无法处理HDR信号的问题：
- **原生10-bit处理**：直接接收PQ（BT.2020）格式HDR帧，避免线性缩放至SDR导致的亮度/色彩信息损失；
- **双域对比学习**：利用SigLIP-2架构，对每帧HDR图像 $x_t$ 及其SDR映射版本 $x_t^{\text{SDR}} = \text{TM}(x_t)$ 进行对比监督：
  $$ \mathcal{L}_{\text{contrast}} = \max\left(0, \delta - D(E_\psi(x_t), E_\psi(c_t)) + D(E_\psi(x_t^{\text{SDR}}), E_\psi(c_t))\right) $$
  其中 $D(\cdot,\cdot)$ 为余弦距离，$c_t$ 为Qwen2.5VL-72B生成的语义描述。该损失确保HDR嵌入与SDR嵌入在语义对齐的同时保持感知距离，防止模态崩溃；
- **联合目标**：总损失 $\mathcal{L}_{\text{enc}} = \mathcal{L}_{\text{Sigmoid}}(x_t, c_t) + \lambda_{\text{ctr}}\mathcal{L}_{\text{contrast}}$ 兼顾语义对齐与HDR鉴别性。

#### 2.2 HDR-Aware Policy Optimization (HAPO)

针对GRPO在模态忽视与token级信用分配上的缺陷，HAPO引入三项HDR专用机制：

**（i）HDR–SDR对比KL散度**
强制策略依赖HDR输入而非SDR或文本先验：
$$ \mathcal{K}_{\text{HDR}}(\theta) = D_{\text{KL}}\left(\pi_\theta^{\text{HDR}} \parallel \pi_\theta^{\text{SDR}}\right) $$
最大化该散度确保移除HDR token后解码分布显著扰动，从互信息视角等价于增强条件互信息 $I_\theta(o; v, v^{\text{SDR}} | v^{\text{SDR}})$。

**（ii）双熵正则化**
防止对比KL最大化导致的熵膨胀（entropy inflation）：
$$ \mathcal{H}_{\text{dual}}(\theta) = \mathbb{E}_{o\sim\pi_\theta^{\text{old}}} \frac{1}{K}\sum_{i,t} \left[\eta_1 \mathcal{H}(\pi_\theta^{\text{HDR}}(o_{i,t})) + \eta_2 \mathcal{H}(\pi_\theta^{\text{SDR}}(o_{i,t}))\right] $$
通过对HDR与SDR双路径的token级熵进行约束，避免策略通过输出过度不确定的分布来虚假满足对比目标。

**（iii）高熵加权（HEW）**
优化token级信用分配：传统GRPO对序列内所有token施加相同优势值 $\hat{A}_i$，而HEW根据每token熵 $H_{i,t}$ 重新加权：
$$ w_{i,t} = \text{clip}\left(\frac{1 + \lambda_{\text{HEW}} H_{i,t}}{\frac{1}{|o_i|}\sum_{t'} H_{i,t'}}, w_{\min}, w_{\max}\right), \quad \tilde{A}_{i,t} = w_{i,t} \cdot \hat{A}_i $$
高熵token通常对应模型需识别HDR特定失真（如色带、高光裁剪）的关键推理步骤，加权后强化这些位置的梯度信号。

**完整HAPO目标函数**
$$ J_{\text{HAPO}}(\theta) = \mathbb{E}_{o\sim\pi_\theta^{\text{old}}}\left[\frac{1}{K}\sum_{i,t} \min\left(\rho_{i,t}\tilde{A}_{i,t}, \text{clip}(\rho_{i,t}, 1-\epsilon, 1+\epsilon)\tilde{A}_{i,t}\right)\right] - \beta D_{\text{KL}}(\pi_\theta^{\text{HDR}} \parallel \pi_{\text{ref}}) + \gamma \mathcal{K}_{\text{HDR}}(\theta) - \mathcal{H}_{\text{dual}}(\theta) $$

#### 2.3 奖励设计与训练流程

**多目标奖励组合**
$$ R_i = w_{\text{fmt}}R_{\text{fmt}} + w_{\text{sc}}R_{\text{sc}} + w_{\text{self}}R_{\text{self}} $$
- **格式奖励** $R_{\text{fmt}}$：确保输出格式合规；
- **高斯回归奖励** $R_{\text{sc}}$：采用高斯加权 $\exp\left(-\frac{(s - s_{\text{pred}})^2}{2\sigma^2}\right)$ 替代硬性阈值，实现细粒度MOS校准；
- **自一致性奖励** $R_{\text{self}}$：通过组内多数投票机制增强推理稳定性。

**两阶段RL训练**
- **阶段一（模态对齐）**：短周期HAPO训练，对齐HDR token与投影层；
- **阶段二（全量RFT）**：在Beyond8Bits完整语料上执行HAPO优化，平衡失真多样性与推理质量。

---

### 3. 实验验证与性能

在Beyond8Bits测试集及跨数据集（LIVE-HDR、SFV+HDR）上的验证表明：
- HDR-Q的PLCC/SRCC显著优于现有HDR专用方法（如HDR-ChipQA、HIDRO-VQA）及MLLM基线（Q-Align、DeQA等）；
- 消融实验证实：移除HDR编码器微调导致性能显著下降；禁用HDR–SDR KL引发模态忽视；去除HEW降低token级推理精度；
- 模型生成简洁的HDR感知推理链（CoT），能有效识别"高光平滑过渡"、"近黑细节保留"、"色带控制"等HDR特定质量线索。

## 实验验证

论文开展了系统的实验验证，涵盖**基准测试、跨域泛化、组件消融及效率分析**四个维度，具体实验内容如下：

---

### 1. 实验设置

**评估数据集**
- **Beyond8Bits**：按源视频身份划分70%/20%/10%训练/验证/测试集，避免内容泄露；
- **LIVE-HDR** 与 **SFV+HDR**：用于零样本（zero-shot）跨数据集泛化测试。

**评价指标**
遵循VQA领域惯例，采用：
- 相关性指标：Spearman秩相关系数（SRCC）、Pearson线性相关系数（PLCC）、Kendall秩相关系数（KRCC），越高越好；
- 误差指标：均方根误差（RMSE），越低越好。

**对比基线**
实验对比四大类方法：
- **传统NR-VQA**：BRISQUE、VBLIINDS、FastVQA、FasterVQA、DOVER、CONVIQT、COVER；
- **HDR专用VQA**：HDRMAX、HDR-ChipQA、HIDRO-VQA；
- **MLLM/VLM质量评估模型**：Q-Align、Q-Instruct、Q-Insight、DeQA、Visual-Quality-R1；
- **基础MLLM**：Qwen2.5-VL(7B)、GLM-4.1V-Thinking(9B)、Ovis2.5(9B)、OmniLong-Qwen2.5-VL(7B)。

**实现配置**
HDR-Q基于Ovis2.5构建，采用rank-4 LoRA适配器；视觉编码器处理原生10-bit PQ格式视频，每片段均匀采样 $T=8$ 帧；HAPO训练参数包括组大小 $K=8$、裁剪系数 $\epsilon=0.1$、参考KL权重 $\beta=0.02$、HDR-SDR对比KL权重 $\gamma=0.5$、HEW调制系数 $\lambda_{\text{HEW}}=0.3$ 等。

---

### 2. 主要结果

**Beyond8Bits数据集性能（表1）**
HDR-Q（full）在所有指标上显著优于现有方法：
- 相比最佳HDR专用方法HIDRO-VQA，SRCC从0.8508提升至0.9206，PLCC从0.8784提升至0.9118，RMSE从6.0875降至5.1594；
- 相比最佳MLLM方法Q-Insight，SRCC相对提升78.1%；
- HDR-Q（SDR）变体（移除HDR编码器微调）性能明显下降（SRCC 0.8914 vs 0.9206），验证HDR感知编码的必要性。

**跨数据集泛化（表2）**
在未见过的LIVE-HDR和SFV+HDR上零样本测试：
- LIVE-HDR：SRCC达0.9081，PLCC达0.8978，显著优于HIDRO-VQA（SRCC 0.8793）；
- SFV+HDR：SRCC达0.7251，PLCC达0.7502，超越所有对比基线；
- 结果证明HDR感知编码器与HAPO grounding产生的表征具有跨域迁移能力。

---

### 3. 消融研究（表3）

通过逐组件移除验证HAPO各机制的贡献：

| 变体 | PLCC | SRCC | RMSE | KRCC | CoT长度 | Token熵 |
|------|------|------|------|------|---------|---------|
| GRPO基线 | 0.79 | 0.81 | 10.73 | 0.56 | 168 | 0.20 |
| + HDR编码器 | 0.81 | 0.83 | 8.96 | 0.61 | 161 | 0.24 |
| HAPO w/o HDR–SDR KL | 0.84 | 0.86 | 7.10 | 0.64 | 142 | 0.29 |
| HAPO w/o 双熵正则 | 0.89 | 0.91 | 5.82 | 0.71 | 148 | 0.26 |
| HAPO w/o HEW | 0.87 | 0.88 | 6.11 | 0.68 | 155 | 0.27 |
| HAPO w/o 自奖励 | 0.90 | 0.92 | 5.22 | 0.71 | 140 | 0.31 |
| **HDR-Q（完整）** | **0.91** | **0.92** | **5.15** | **0.72** | **137** | **0.33** |

关键发现：
- **HDR编码器**：移除导致SRCC下降约0.09，确认10-bit信号处理的重要性；
- **HDR–SDR对比KL**：移除引发模态忽视（modality neglect），性能显著下降；
- **双熵正则化**：禁用后Token熵降低（0.26 vs 0.33），伴随推理不稳定；
- **HEW（高熵加权）**：移除后CoT长度增加（155 vs 137）且精度下降，证明token级信用分配的有效性；
- **自奖励**：移除降低模型在噪声样本上的稳定性。

---

### 4. 推理行为分析

**推理链（CoT）演化（图7）**
- **CoT长度**：随HAPO训练迭代逐渐缩短（从初始约168 token降至137 token），表明模型减少冗余推理，直接基于HDR视觉证据判断；
- **Token熵**：同步上升（从0.20升至0.33），说明HEW机制成功将优化重点导向高信息量（高不确定性）的关键token，而非均匀优化所有token。

**定性案例（图6）**
对比Ovis 2.5与HDR-Q对同一段HDR视频的推理：
- **Ovis 2.5**：产生矛盾判断（先评95分后改82分），推理基于错误观察（误判"左侧树木欠曝"实际为正常HDR高光），且未识别HDR-specific失真；
- **HDR-Q**：准确识别"高光平滑过渡无裁剪"、"色彩稳定无偏移"、"雾气区域细节柔和符合自然氛围"等HDR质量线索，给出一致且物理合理的评分（82分）。

---

### 5. 计算效率分析（6.4节）

- **训练开销**：HAPO仅在训练阶段增加SDR路径的前向传播（额外一次编码器前向），计算 overhead 可控；
- **推理成本**：与标准单路径解码相同，未引入额外计算，在NVIDIA H200 GPU上保持竞争性的吞吐率。

## 未来工作

基于论文的技术路线与实验发现，以下方向具有进一步探索价值：

---

### 1. 多格式HDR与自适应色调映射
当前Beyond8Bits与HDR-Q主要针对**PQ（ST 2084）+ BT.2020**格式。未来可扩展至：
- **HLG（Hybrid Log-Gamma）**与**Dolby Vision**（动态元数据）的质量评估，探索不同传输曲线对感知失真的影响差异；
- **显示自适应评估**：针对不同峰值亮度（400 nits vs 1,000 nits vs 4,000 nits）与对比度特性（OLED vs Mini-LED）的显示设备，建立条件化质量模型 $Q(v|\mathcal{D}_{\text{display}})$，而非单一MOS。

---

### 2. 时序动态与HDR特定伪影建模
论文提及的曝光闪烁（exposure flicker）与色调映射时序不一致（temporal tone-mapping inconsistencies）在HDR-UGC中显著，但当前方法主要基于帧级特征聚合：
- **时序敏感架构**：引入时序卷积或状态空间模型（SSM）显式建模HDR视频的亮度变化连续性；
- **闪烁检测专项任务**：将闪烁强度作为独立维度与整体MOS联合预测，提升对UGC手持拍摄场景的鲁棒性。

---

### 3. 生成式HDR内容（AIGC-HDR）的质量评估
随着HDR生成模型（如HDR视频超分、HDR合成、文本到HDR视频生成）的发展：
- **无参考生成质量评估**：扩展HDR-Q至生成内容领域，处理生成式伪影（如GAN-based HDR中的亮度不自然、扩散模型中的细节幻觉）；
- **提示对齐评估**：评估生成HDR内容与文本提示在亮度范围、色彩氛围上的一致性。

---

### 4. 计算效率与实时优化
- **训练效率**：HAPO的双路径（HDR+SDR）前向传播在训练时计算成本倍增，可探索**知识蒸馏**或**单路径对比估计**（如通过dropout masking模拟SDR路径）以降低开销；
- **边缘部署**：针对移动端HDR视频上传场景的实时质量预筛选，开发轻量级HDR感知编码器（如MobileNet-style架构）与模型量化方案。

---

### 5. 跨模态与沉浸式扩展
- **音视频联合HDR评估**：HDR视频常伴随空间音频（Dolby Atmos），建立视听联合质量模型，探索亮度-响度交互效应（如HDR高光与音频动态范围的感知关联）；
- **360°/VR HDR视频**：结合等距柱状投影（equirectangular）与视口（viewport）渲染特性，评估沉浸式HDR内容的观看体验质量。

---

### 6. 可解释性与交互式修正
- **细粒度失真定位**：将HDR-Q的CoT推理与像素级/区域级质量图（quality map）结合，实现"高光区域过曝扣5分"的可视化解释；
- **人在回路优化**：基于HDR-Q的评分置信度（entropy-based uncertainty）主动采样需要人工复核的边界样本，构建主动学习循环以迭代优化数据集。

---

### 7. 理论深化
- **HEW的信息论界限**：将高熵加权（HEW）与**信息瓶颈（Information Bottleneck）**理论关联，形式化证明在感知质量任务中最优的token压缩-预测权衡；
- **对比KL的变分界限**：扩展第5.2节的互信息视角，探索更紧致的变分下界以指导HDR-SDR对比学习的架构设计。

---

### 8. 文化差异与个性化感知
当前众包数据主要反映西方/英语用户群体的感知偏好：
- **跨文化HDR感知**：探索不同文化背景对HDR亮度偏好（如高对比度 vs 柔和色调）的差异，构建地域自适应的质量模型；
- **个性化评估**：结合用户历史观看数据与显示环境（环境光照、屏幕反射），实现个性化的HDR质量预测。

## 总结

该论文针对**高动态范围（HDR）用户生成内容（UGC）视频的质量评估**这一未充分探索的领域，提出了当前规模最大的主观数据集与首个专用多模态大语言模型（MLLM），核心内容可概括如下：

**1. 问题与动机**
现有视频质量评估（VQA）模型主要针对标准动态范围（SDR）设计，难以处理HDR视频特有的高比特深度、宽色域及扩展亮度范围所暴露的失真（如近黑压碎、高光裁剪、色带与曝光闪烁）。此外，现有HDR数据集规模小且多限于专业内容，而MLLM直接应用于HDR-UGC时面临**模态忽视**（忽视HDR视觉输入依赖文本先验）与**细粒度回归困难**等挑战。

**2. Beyond8Bits数据集**
构建了迄今最大的真实世界HDR-UGC质量数据集，包含来自6,861个独立源的约**44,000段视频**，经严格HDR元数据验证（PQ传输、10-bit、BT.2020）与真实压缩阶梯（0.2–5 Mbps）处理。通过Amazon Mechanical Turk收集超过**150万**人工评分，采用SUREAL方法校正主观偏差，覆盖多样化场景与设备，为HDR感知模型训练提供基础。

**3. HDR-Q模型与HAPO优化框架**
提出首个专为HDR-UGC设计的MLLM **HDR-Q**，核心创新包括：

- **HDR感知视觉编码器**：基于SigLIP-2适配，直接处理原生10-bit PQ信号（无SDR色调映射），通过**双域对比损失** $\mathcal{L}_{\text{contrast}} = \max(0, \delta - D(E_\psi(x_t), E_\psi(c_t)) + D(E_\psi(x_t^{\text{SDR}}), E_\psi(c_t)))$ 确保HDR嵌入既保留语义对齐又具备HDR鉴别性，避免与SDR嵌入崩溃。

- **HDR感知策略优化（HAPO）**：扩展GRPO强化学习框架，通过三项机制解决模态忽视与推理稳定性：
  - **HDR–SDR对比KL**：最大化 $\mathcal{K}_{\text{HDR}}(\theta) = D_{\text{KL}}(\pi_\theta^{\text{HDR}} \parallel \pi_\theta^{\text{SDR}})$，强制策略依赖HDR输入而非SDR或文本先验；
  - **双熵正则化**：约束HDR与SDR双路径的token级熵 $\mathcal{H}_{\text{dual}}(\theta)$，防止对比KL最大化导致的熵膨胀；
  - **高熵加权（HEW）**：按token熵 $H_{i,t}$ 重新加权优势值 $\tilde{A}_{i,t} = w_{i,t} \cdot \hat{A}_i$，将优化重点导向识别HDR失真的关键推理步骤。

  完整目标函数整合上述项与分组相对策略优化：
  $$ J_{\text{HAPO}}(\theta) = \mathbb{E}\left[\frac{1}{K}\sum_{i,t} \min(\rho_{i,t}\tilde{A}_{i,t}, \text{clip}(\rho_{i,t}, 1-\epsilon, 1+\epsilon)\tilde{A}_{i,t})\right] - \beta D_{\text{KL}}(\pi_\theta^{\text{HDR}} \parallel \pi_{\text{ref}}) + \gamma \mathcal{K}_{\text{HDR}}(\theta) - \mathcal{H}_{\text{dual}}(\theta) $$

**4. 实验验证**
在Beyond8Bits及公共基准（LIVE-HDR、SFV+HDR）上的实验表明：
- HDR-Q在PLCC、SRCC、RMSE等指标上**显著优于**现有HDR专用方法（如HIDRO-VQA）及MLLM基线（如Q-Align、DeQA），实现SOTA性能；
- 零样本跨数据集测试验证了良好的泛化能力；
- 消融实验证实：移除HDR编码器微调导致性能显著下降；禁用HDR–SDR KL引发模态忽视；HEW有效缩短推理链长度并提升token级决策精度。

**5. 结论**
该工作通过大规模真实数据与HDR专用MLLM架构，解决了HDR-UGC质量评估中的数据稀缺与模态对齐难题，为HDR感知计算、视频编码优化及生成式HDR内容评估奠定了新基础。



Q7: 想要进一步了解论文


以上只是了解一篇论文的几个基本FAQ。如果你还想与Kimi进一步讨论该论文，请点击 这里  为你跳转Kimi AI网页版，并启动一个与该论文相关的新会话。
