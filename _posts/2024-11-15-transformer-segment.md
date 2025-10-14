---
layout: post
title: 基于Transformer的分割模型解析——SETR、Segmenter、SegFormer与MaskFormer
date: 2024-11-15 20:30:00
description: 从 ViT 发展而来的语义分割模型，包括 SETR、Segmenter、SegFormer，以及实例分割的 MaskFormer/Mask2Former，解析其编码器、解码器与核心机制。
thumbnail: /assets/img/blogs/transformer-segment/cover.jpg
tags: [计算机视觉, Transformer, 图像分割, ViT, SegFormer, MaskFormer]
categories: 研究笔记
---

在前几篇文章中，我们介绍了 **CNN 系列模型 → 高效轻量化分割 BiSeNetv2 → ViT 与 MAE**。  
它们分别在局部细节捕捉与全局建模能力上奠定了基础。  

然而在语义分割任务中，我们不仅需要全局上下文，还希望 **恢复图像边界和多尺度信息**。  
为此，基于 Transformer 的分割模型应运而生。本文将梳理典型模型的设计逻辑与改进策略。

---

#### **📌 SETR（SEgmentation TRansformer）**

以 ViT 为 backbone 的首个语义分割代表模型。  
在 ViT 编码器基础上，SETR 提出三种解码器策略：

<div style="text-align:center">
  <img src="/assets/img/blogs/transformer-segment/image-20250909173759174.png" alt="SETR结构" style="max-width:85%; height:auto;" data-zoomable>
</div>

1. **双线性插值**：简单上采样恢复分辨率  
2. **渐进式上采样**：交替卷积 + 2 倍上采样，避免噪声累积  
3. **多级特征融合（MLA）**：  
   - 从 Transformer 不同层抽取 M 个特征  
   - 1D → 2D 重整 → 三次卷积（通道数逐步减半）  
   - 上采样 → 通道级拼接 → 双线性上采样到原图尺寸  

---

#### **📌 Segmenter**

<div style="text-align:center">
  <img src="/assets/img/blogs/transformer-segment/Segmenter.png" alt="Segmenter结构" style="max-width:85%; height:auto;" data-zoomable>
</div>

- 编码器同 SETR，使用 ViT  
- 解码器使用 **注意力机制**：  
  - 类别嵌入向量主动查询图像嵌入特征  
  - 通过自注意力计算 Mask，每个类别在每个 Patch 的语义匹配分数  
  - 上采样回原图尺寸，生成像素级预测  

---

#### **📌 SegFormer：简单高效的 Transformer 分割设计**

<div style="text-align:center">
  <img src="/assets/img/blogs/transformer-segment/Segformer.png" alt="SegFormer结构" style="max-width:85%; height:auto;" data-zoomable>
</div>

- 层次化 Transformer 编码器输出 **多尺度特征**  
- MLP 解码器聚合不同层级信息  
- 改进 ViT 局限性：  
  - 支持多尺度特征，捕捉上下文信息  
  - 更灵活的位置信息融合  

---

#### **📌 ViT 局限性回顾**

1. Patch 固定 → 单一特征尺寸 → 缺乏多尺度表征，边界恢复不精确  
2. 计算量大 → 自注意力平方级复杂度  
3. 固定尺寸位置编码 → 输入灵活性差  

---

#### **📌 SegFormer 编码器改进策略**

1. **逐层下采样**（Overlap Patch Merge）  
   - 保持 patch 周围局部连续性  
   - 第一次 4 倍，下采样后每次 2 倍，最终缩减 32 倍  
2. **Efficient Self-Attention**：  
   - 对 K 特征添加缩放因子 R  
   - Resize 缩小维度后全连接恢复，降低计算  
3. **Mix-FFN**：  
   - 前馈网络中融合 3×3 卷积  
   - 注入位置信息，提高空间感知能力  

---

#### **📌 SegFormer 解码器设计**

- 四层不同尺度特征  
- 线性层映射到统一维度 D  
- 双线性插值至 H/4 × W/4  
- 通道级拼接 → 线性映射 → 输出类别维度

---

#### **📌 实例分割 MaskFormer**

<div style="text-align:center">
  <img src="/assets/img/blogs/transformer-segment/MaskFormer.jpg" alt="MaskFormer结构" style="max-width:85%; height:auto;" data-zoomable>
</div>

- **传统方法**：像素级预测 → 连通区域聚类 → 掩码生成  
  - 优势：结构简单，小物体敏感  
  - 局限：依赖局部感受野，难以建模长程依赖  
- **MaskFormer**：自顶向下掩码级推理  
  - 通过 Transformer 注意力计算像素关联  
  - 动态生成类别掩码 → 高精度实例分割  

**处理流程**：  
1. Image feature → 聚类 Mask  
2. 类似 DETR 的 preset queries → MSA → Q 对齐 per-pixel embedding  
3. 点积得到 pixel-level mask prediction  
4. 联合优化：分类 + 掩码损失（IoU + 样本平衡）  

---

#### **📌 Mask2Former**

<div style="text-align:center">
  <img src="/assets/img/blogs/transformer-segment/Mask2Former.png" alt="Mask2Former结构" style="max-width:85%; height:auto;" data-zoomable>
</div>

- 基础结构不变，Transformer decoder 优化三方面：  
  1. **Masked Cross-Attention**：加入上一层掩码，关注重要区域  
  2. **多尺度特征输入**：加入可学习尺度嵌入  
  3. **Self-Attention 放在 Cross-Attention 后**，X0 查询特征可学习，删除 Dropout  

> 这种设计优化了查询初始化，结合位置编码引导 Transformer 学习特征，提高实例分割精度和稳定性。

---

#### **📌 总结**

- SETR：首个 ViT 分割尝试，多解码器设计  
- Segmenter：类别向量主动查询图像特征，生成 Mask  
- SegFormer：多尺度特征 + 高效编码器，解决 ViT 局限  
- MaskFormer/Mask2Former：实例分割的新范式，自顶向下动态掩码生成  

> **一句话记住**：基于 Transformer 的分割模型从 ViT 出发，不断优化编码器和解码器结构，兼顾全局建模、多尺度特征和实例掩码预测，实现语义与实例分割的新高度。

---

<div class="post-navigation" style="margin-top:2rem; padding-top:1rem; border-top:1px solid #eaeaea;">
  <p style="margin-bottom:0.5rem; font-weight:600;">📖 系列文章导航</p>
  <ul style="list-style:none; padding-left:0;">
    <li>⬅️ 上一篇：<a href="{{ '/blog/2024/transformer-backbone-vit' | relative_url }}">基于Transformer的视觉Backbone——ViT与MAE</a></li>
    <li>➡️ 下一篇：<a href="{{ '/blog/2024/clip' | relative_url }}">多模态视觉大模型解析——CLIP</a></li>
  </ul>
</div>
