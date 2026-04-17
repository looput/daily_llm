# Self-Calibrating Language Models via Test-Time Discriminative Distillation

**arXiv**: [2604.09624](https://arxiv.org/abs/2604.09624) · [PDF](https://arxiv.org/pdf/2604.09624)  
**领域**: Hallucination  
**作者**: Hedna, Strich, Semmann, Biemann  
**综合评分**: 8.36  （novelty: 9.0 · method: 8.5 · evidence: 8.5 · clarity: 8.0）

---

## 摘要

> 本文提出了一种名为SECL（自校准语言模型）的新方法，通过测试时间判别蒸馏实现语言模型的自我校准。该方法创新性地利用LLMs内部已存在的、比其口头表达更准确的校准信号（即模型回答“这个答案正确吗？”时“True”的标记概率），作为无需标签的自监督信号。SECL仅在输入分布发生变化时进行自适应训练，在四个不同领域的小型语言模型上，将预期校准误差降低了56-78%，性能优于其自身的监督信号，并与最近的推理时间方法相当或更优。这是首个将测试时间训练应用于校准任务的方法。

---
