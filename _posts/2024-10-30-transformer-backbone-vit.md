---
layout: post
title: 基于Transformer的视觉Backbone——ViT与MAE
date: 2024-10-30 20:30:00
description: 在 CNN → 高效轻量化分割 → Transformer 的发展脉络下，ViT与MAE为视觉任务提供了全局建模能力，并为后续分割、识别任务奠定基础。
thumbnail: /assets/img/blogs/transformer-backbone-vit/cover.jpg
tags: [计算机视觉, 图像分割, 图像处理, Transformer, ViT, MAE, CV Backbone]
categories: 研究笔记
---

在前几篇文章中，我们依次介绍了 **CNN 系列语义分割模型**（FCN → U-Net → DeepLab）以及 **高效轻量化分割模型 BiSeNetv2**。  
它们在不同阶段提升了分割精度与效率，但全局建模能力仍有限。  

随着 **Transformer** 的引入，视觉模型可以突破卷积的局部性限制，通过自注意力机制直接捕捉图像中长程依赖。  
在视觉领域，ViT 与 MAE 是两个典型的 Transformer Backbone 案例，本文将详细解析它们的工作原理与核心机制。

---

#### **📌 基于 Transformer 的 CV Backbone — ViT**

核心思想：**将图像划分为小块（Patch）**，每个小块视作“单词”，整个图像看作一个由 Patch 组成的序列，通过 Transformer 编码器进行处理。

<div style="text-align:center">
  <img src="/assets/img/blogs/transformer-backbone-vit/image-20250909163833271.png" alt="ViT结构示意图" style="max-width:85%; height:auto;" data-zoomable>
</div>

主要操作流程如下：

1. **图像切块与嵌入（Patch Embedding）**  
   - 将输入图像切分为大小为 16×16 的 Patch，每个 Patch 展平并映射到特征维度 768  
   - 增加一个 **CLS Token**，用于聚合图像全局特征  
   - 得到序列维度为 `197 × 768`，并将 **位置编码** 与 Patch 特征相加

2. **Transformer 编码器（Encoder）**  
   - 每层包括：归一化 → 多头自注意力 → 残差连接 → 归一化  
   - **多头自注意力**：特征拆分到不同子空间，分别计算 QKV，输出拼接形成全局特征

3. **前馈网络 MLP（Feed Forward Network）**  
   - 两层全连接：先放大 → ReLU 激活 → 再缩小  
   - 放大操作增强特征表达能力，使非线性变换可以学习更复杂特征

4. **Transformer Block 堆叠**  
   - 多个 Block 顺序堆叠，实现深层特征抽象与全局上下文建模

5. **线性分类层**  
   - 使用 CLS Token 的最终表示进行分类  
   - 对比平均池化：CLS Token 是主动、动态、有选择的全局聚合，平均池化被动、静态、无差别

---

#### **📌 核心知识点**

**为什么图像可以当作序列处理？**  
自注意力机制本质是计算序列中任意两个元素之间的关系。  
对于图像而言，远距离 Patch 之间的关系可能同样重要，自注意力可以建模全局依赖，赋予模型“一眼看全图”的能力。

---

#### **📌 ViT 的局限性**

1. **Patch 固定大小**  
   - 对不同分辨率图像或分割任务不够灵活  
   - 单一特征尺寸 → 缺乏多尺度表征 → 难以精准恢复细节边界  
   - Patch 数量不一致 → 需要插值位置编码 → 精度损失

2. **计算量较大**  
   - 自注意力计算复杂度为 $O(N^2)$，随着 Patch 数量增加呈平方级增长

---

#### **📌 基于 Transformer 的 CV Backbone — MAE**

MAE（Masked Autoencoder）在 ViT 基础上引入**随机遮盖机制**：

1. 操作流程与 ViT 相同，但随机遮盖部分 Patch（例如 75%）  
2. 剩余 Patch 经过 Transformer 编码器处理  
3. 输出维度为 `(196 * 0.25 + 1) * 768 ≈ 50 × 768`  
4. 编码器学会从少量可见 Patch 重建原图，增强表征能力

这种遮盖策略有效提高了 **数据效率**，同时降低计算开销，尤其适合大规模预训练。

---

#### **📌 总结**

- **ViT**：将图像切块为序列，通过 Transformer 捕捉全局依赖  
- **MAE**：在 ViT 上增加随机遮盖，提高特征学习效率与泛化能力  
- **核心优势**：全局建模能力强，能够捕捉长程依赖  
- **局限**：Patch 尺寸固定、计算复杂度高，需要多尺度和效率优化  

> **一句话记住**：ViT 让视觉模型可以“看全局”，MAE 让模型在部分信息下仍能高效学习全局特征。

---

<div class="post-navigation" style="margin-top:2rem; padding-top:1rem; border-top:1px solid #eaeaea;">
  <p style="margin-bottom:0.5rem; font-weight:600;">📖 系列文章导航</p>
  <ul style="list-style:none; padding-left:0;">
    <li>⬅️ 上一篇：<a href="{{ '/blog/2024/transformer-intro' | relative_url }}">Transformer架构浅述</a></li>
    <li>➡️ 下一篇：<a href="{{ '/blog/2024/transformer-segment' | relative_url }}">基于Transformer的分割模型解析——SETR、Segmenter、SegFormer与MaskFormer</a></li>
  </ul>
</div>
