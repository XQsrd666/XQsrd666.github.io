---
layout: post
title: Transformer架构浅述
date: 2024-10-20 20:30:00
description: Transformer 以自注意力机制为核心，突破了卷积的局部性限制，能够直接建模全局依赖。在语义分割的发展脉络中，它代表着从高效CNN迈向全局建模的新阶段。
thumbnail: /assets/img/blogs/transformer-intro/cover.jpg
tags: [图像分割, 图像处理与视觉算法, Transformer, 计算机视觉]
categories: 研究笔记
---

在前几篇文章中，我们介绍了 **CNN 主导的语义分割模型**（FCN → U-Net → DeepLab），以及 **高效轻量化分割模型 BiSeNetv2**。  
这些方法在各自阶段推动了分割任务的发展，但仍存在一个核心局限：**全局建模能力不足**。

CNN 的感受野需要通过逐层堆叠卷积才能扩大，本质仍是**局部逐级建模**；即便引入空洞卷积或金字塔池化，也只能近似解决全局依赖问题。  
而在语义分割中，很多场景需要理解图像整体的布局与长程关系，例如“草地上的牛”和“海上的船”，单纯依赖卷积往往力不从心。

👉 在这样的背景下，**Transformer** 进入了视觉领域。  
它以 **自注意力机制** 为核心，使序列中任意两个元素可以直接交互，天然具备全局建模能力。本文将简要介绍 Transformer 的核心架构与原理，为后续的 **基于 Transformer 的分割模型（如 ViT、SegFormer）** 打下基础。

---

#### **📌 Transformer整体架构**

Transformer 最早由 [Attention is All You Need, 2017] 提出，用于机器翻译任务。  
它完全抛弃了 RNN 和 CNN，直接基于 **自注意力机制（Self-Attention）** 建模序列中任意位置的依赖关系。

整体结构由 **编码器（Encoder）** 和 **解码器（Decoder）** 组成：  

<div style="text-align:center">
  <img src="/assets/img/blogs/transformer-intro/Transformer_full_architecture.png" alt="Transformer整体架构" style="max-width:90%; height:auto;" data-zoomable>
</div>

---

##### **1. 编码器（Encoder）**

输入序列（如一句话的单词或图像划分的 patch）经过 **嵌入（Embedding）** 和 **位置编码（Positional Encoding）**，送入多层编码器堆叠。  

每层编码器包含两个核心模块：  
1. **多头自注意力（Multi-Head Self-Attention, MSA）**  
2. **前馈神经网络（Feed Forward Network, FFN）**

编码器的作用是：**在序列内部进行全局信息交互，得到更抽象、更语义化的表示**。

---

##### **2. 解码器（Decoder）**

解码器在自然语言生成中用于逐步生成目标序列。结构类似编码器，但增加了两次注意力机制：  
1. **Masked Multi-Head Attention**：保证当前时刻只能看到已生成的内容。  
2. **Cross-Attention**：使用解码器状态作为 Query，编码器输出作为 Key/Value，实现“查询源信息”。  

在视觉任务中（如分割），通常只保留 **编码器部分**，直接输出像素级预测，不需要解码器。

---

#### **📌 自注意力机制（Self-Attention）**

Transformer 核心是自注意力机制，每个元素都可以与序列内任意其他元素直接交互。  

输入序列 $X$ 经过线性映射得到 **查询向量 Q、键向量 K、值向量 V**：  

$$
Q = X W^Q, \quad K = X W^K, \quad V = X W^V
$$

注意力分数通过点积计算：

$$
\text{Attention}(Q, K, V) = \text{softmax}\left(\frac{Q K^T}{\sqrt{d_k}}\right) V
$$

直观理解：  
- 每个元素会计算与所有元素的相关性；  
- 根据相关性加权求和，得到融合全局信息的新表示。

---

#### **📌 多头注意力（Multi-Head Attention）**

单一注意力机制可能只关注一种关系，而复杂任务需要考虑多维依赖。  
于是 Transformer 使用 **多头注意力**：  

- 将输入拆分为多个子空间（子维度）；  
- 在每个子空间内独立计算注意力；  
- 最后拼接所有子结果。  

<div style="text-align:center">
  <img src="/assets/img/blogs/transformer-intro/image-20250908170117006.png" alt="多头注意力示意图" style="max-width:85%; height:auto;" data-zoomable>
</div>

这样可以让模型从不同角度捕捉依赖关系，提升表达能力。

---

#### **📌 位置编码（Positional Encoding）**

与 CNN 不同，Transformer **没有内置顺序感**，因此需要显式加入位置信息：  

- **固定式**：利用正弦/余弦函数生成位置向量  
- **可学习式**：将位置向量作为参数随训练更新  

在视觉任务中，常将图像切分为 patch，再加位置编码以保持空间结构。

---

#### **📌 残差连接与前馈网络**

每个注意力层和前馈层都使用 **残差连接（Residual Connection） + 层归一化（LayerNorm）**，防止梯度消失并加快收敛。  

前馈网络通常为两个全连接层，中间带激活函数（如 ReLU、GELU），用于增强特征表达能力。

---

#### **📌 Transformer 与 CNN 对比**

**CNN**：  
- 核心：局部卷积 + 权值共享  
- 特点：空间局部性 + 平移不变性（归纳偏置强）  
- 优势：参数高效，计算快，擅长捕获局部细节  
- 局限：全局建模能力弱，需要逐层堆叠扩大感受野  

**Transformer**：  
- 核心：自注意力机制  
- 特点：任意两个位置直接交互（归纳偏置弱）  
- 优势：擅长长程依赖、复杂语义关联  
- 局限：计算复杂度高，需要大量数据  

**总结**：两者互补。现代视觉模型趋势是：  
- CNN 融入注意力机制 → 增强全局感知  
- Transformer 融入卷积 → 降低计算开销  

---

#### **📌 总结**

Transformer 的引入，让语义分割从 **局部逐层建模（CNN）** 进入了 **全局直接建模（Attention）** 的新阶段：  

- **自注意力机制**：实现任意元素交互，打破感受野限制  
- **多头注意力 + 位置编码**：捕捉多维关系，保持空间结构  
- **视觉应用**：为 SegFormer、Mask2Former、多模态分割模型打下基础  

> **一句话记住**：CNN 擅长“局部细节”，Transformer 擅长“全局依赖”，两者结合是视觉分割未来发展方向。

---

<div class="post-navigation" style="margin-top:2rem; padding-top:1rem; border-top:1px solid #eaeaea;">
  <p style="margin-bottom:0.5rem; font-weight:600;">📖 系列文章导航</p>
  <ul style="list-style:none; padding-left:0;">
    <li>⬅️ 上一篇：<a href="{{ '/blog/2024/bisenetv2' | relative_url }}">高效语义分割——BiSeNetv2的网络设计与训练策略</a></li>
    <li>➡️ 下一篇：<a href="{{ '/blog/2024/transformer-backbone-vit' | relative_url }}">基于Transformer的视觉Backbone——ViT与MAE</a></li>
  </ul>
</div>
