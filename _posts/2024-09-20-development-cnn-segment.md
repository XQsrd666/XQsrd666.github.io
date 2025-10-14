---
layout: post
title: 语义分割发展历程——FCNN、SegNet、U-Net、PSPNet与DeepLab
date: 2024-09-20 20:30:00
description: 从FCN到DeepLab，全面梳理语义分割模型的演化、特点与技术细节，帮助理解空间细节与上下文语义如何结合。
thumbnail: /assets/img/blogs/development-cnn-segment/cover.jpg
tags: [图像分割, 图像处理与视觉算法, 计算机视觉]
categories: 研究笔记
---

语义分割作为计算机视觉的核心任务之一，其目标是实现**像素级的分类**，即不仅识别图像中“有什么物体”，还要确定它们在图像中的**具体位置**。在上一篇文章中，我们介绍了语义分割的基本概念以及空间细节信息与上下文语义信息的重要性。

本篇文章聚焦于语义分割发展初期的 CNN 基础阶段，系统梳理从 FCN 到 SegNet、U-Net、PSPNet 和 DeepLab 系列的演进过程，帮助理解这些模型的设计思路、技术亮点与局限性

---

#### **📌 1. FCNN：全卷积神经网络的开创**

传统卷积神经网络通常由 `5 层卷积（特征提取） + 3 层全连接（结果映射）` 组成，而 FCNN（Fully Convolutional Neural Network）提出了一个突破：

- 将全连接层替换为同尺寸卷积层，输出尺寸为输入的 1/32。  
- 通过**反卷积上采样**逐步恢复至原始尺寸，实现端到端的像素级预测。  

**具体流程示意**：

1. out7 全卷积层映射到样本标记空间 → 二倍上采样  
2. out4 全卷积层映射到样本标记空间，与上一步结果相加 → 再二倍上采样  
3. out3 全卷积层映射到样本标记空间，与上一步结果相加 → 最终八倍上采样  

<div style="text-align:center">
  <img src="/assets/img/blogs/development-cnn-segment/image-20250904163856107.png" alt="FCN流程图" style="max-width:90%; height:auto;" data-zoomable>
</div>

**特点**：

- **开创性**：首次提出端到端像素级预测。  
- **跳跃连接 + 反卷积**：保留浅层空间信息，与深层语义特征结合。  
- **局限性**：八倍上采样较粗糙，分类是“粗暴的像素分类”，缺乏像素间关系，空间一致性不足。

---

#### **📌 2. SegNet：编码器-解码器的反池化策略**

SegNet 提出了一个基于 **Encoder-Decoder（编码器-解码器）** 的分割网络架构，引入**反池化（Unpooling）**上采样策略：

- **编码器**：输入图像 → 特征图谱（VGG16 前 13 层）  
- **解码器**：基于编码器特征图谱进行像素级分类  

解码器的核心创新：**记录池化索引位置**，在上采样时使用 Unpooling 代替直接反卷积。  

<div style="text-align:center">
  <img src="/assets/img/blogs/development-cnn-segment/image-20250904202323495.png" alt="SegNet架构图" style="max-width:90%; height:auto;" data-zoomable>
</div>

**特点**：

- 更准确地反映物体边界  
- 边界信息更清晰，但邻近信息缺失会略微影响精度  
- 内存占用降低，适用于实际应用场景  

---

#### **📌 3. U-Net：跳跃连接与多尺度融合**

U-Net 的设计理念：

- **收缩路径**（编码器）：捕获图像特征  
- **扩展路径**（解码器）：恢复空间分辨率，保持边界信息  

编码器结构：4 个模块，每个模块包含两个卷积层 + 一个步长为 2 的最大池化层  
解码器结构：将对应编码器特征层复制并裁剪后与解码器特征拼接，再上采样  

<div style="text-align:center">
  <img src="/assets/img/blogs/development-cnn-segment/image-20250904211842570.png" alt="U-Net结构图" style="max-width:90%; height:auto;" data-zoomable>
</div>

**特点**：

- 通过 concatenation 保留空间细节和深层语义  
- 边界清晰，像素级预测精度高  
- 计算量和内存消耗较大，但少量数据即可进行端到端训练  

---

#### **📌 4. PSPNet：金字塔场景解析**

PSPNet 引入 **Pyramid Scene Parsing Module（金字塔池化模块）**：

- **编码器**：ResNet backbone  
- **训练辅助**：Auxiliary loss 提升 ResNet 特征学习能力  
- **解码器**：对 f5 特征层进行多尺度平均池化（1×1、2×2、3×3、6×6） → resize → 拼接 → 卷积 → 全连接  

<div style="text-align:center">
  <img src="/assets/img/blogs/development-cnn-segment/image-20250905161010755.png" alt="PSPNet金字塔池化" style="max-width:90%; height:auto;" data-zoomable>
</div>

<div style="text-align:center">
  <img src="/assets/img/blogs/development-cnn-segment/image-20250905161050873.png" alt="PSPNet多尺度融合" style="max-width:90%; height:auto;" data-zoomable>
</div>

**技术细节**：

- 双线性插值平滑信息，计算量小  
- 反卷积可学习，但易产生伪影  
- 核心任务：捕获多尺度全局上下文信息，而非精确重建物体边界  

---

#### **📌 5. DeepLabV3：空洞卷积与ASPP**

DeepLab 系列进一步提升了分割精度，核心在于：

- **Backbone + ASPP**：Backbone 提取低级和高级特征，ASPP 捕获多尺度上下文  
- **空洞卷积**：通过膨胀率增加感受野，不损失分辨率  
- **解码器**：融合低级特征和 ASPP 输出，插值上采样到原始尺寸  

<div style="text-align:center">
  <img src="/assets/img/blogs/development-cnn-segment/image-20250905172609520.png" alt="DeepLabV3结构图" style="max-width:90%; height:auto;" data-zoomable>
</div>

**特点**：

- 空洞卷积扩大感受野，捕获远距离上下文  
- Decoder 融合低分辨率细节，提高预测精度  
- 并行空洞卷积计算复杂度高，但性能显著  

---

#### **📌 6. 模型演化总结**

| 模型 | 核心技术 | 优点 | 局限 |
|------|----------|------|------|
| FCN | 全卷积 + 跳跃连接 | 端到端像素预测 | 上采样粗糙，缺乏空间一致性 |
| SegNet | 编码器-解码器 + Unpooling | 内存低，边界改善 | 邻近信息丢失，精度略低 |
| U-Net | 跳跃连接 + 拼接 | 精确边界，高精度 | 内存消耗大，计算量大 |
| PSPNet | 金字塔池化 | 多尺度上下文，低计算量 | 细节恢复有限 |
| DeepLabV3 | 空洞卷积 + ASPP | 多尺度上下文 + 高精度 | 计算复杂度高 |

✅ 总结规律：

- 模型演化核心思想：**逐步结合空间细节与上下文语义信息**  
- 技术手段不断创新：跳跃连接 → Unpooling → 拼接 → 金字塔池化 → 空洞卷积  
- 目的是在保证边界精度的同时，捕获全局上下文信息  

---

#### **📌 结语**

语义分割的发展，体现了 **计算机视觉对空间与语义信息理解的不断深化**。从 FCN 的初步尝试，到 DeepLabV3 的多尺度上下文融合，每一步都在探索如何让模型既“分得准”，又“分得对”。

在后续文章中，将针对BiSeNetv2展开介绍。

> **一句话总结**：语义分割的本质是“空间细节 + 上下文语义”，任何改进模型的努力都是在强化这两者的结合。

---

<div class="post-navigation" style="margin-top:2rem; padding-top:1rem; border-top:1px solid #eaeaea;">
  <p style="margin-bottom:0.5rem; font-weight:600;">📖 系列文章导航</p>
  <ul style="list-style:none; padding-left:0;">
    <li>⬅️ 上一篇：<a href="{{ '/blog/2024/segment-background' | relative_url }}">语义分割入门——你必须掌握的核心概念</a></li>
    <li>➡️ 下一篇：<a href="{{ '/blog/2024/bisenetv2' | relative_url }}">高效语义分割——BiSeNetv2的网络设计与训练策略</a></li>
  </ul>
</div>
