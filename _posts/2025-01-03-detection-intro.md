---
layout: post
title: 目标检测基础——Two-Stage与One-Stage方法
date: 2025-01-03 12:00:00
description: 目标检测算法概述，包括检测任务引入、Two-Stage与One-Stage方法、滑动窗口、选择性搜索和RPN的基本原理与网络结构。
thumbnail: /assets/img/blogs/detection-intro/cover.jpg
tags: [计算机视觉, 目标检测, R-CNN, YOLO, RPN, SSD]
categories: 研究笔记
---

在计算机视觉中，**目标检测**任务的核心是同时完成两个目标：

1. **物体分类**：识别图像中每个目标的类别  
2. **位置定位**：确定物体在图像中的准确位置（通常以边界框表示）  

---

#### **📌 目标检测算法分类**

根据检测流程，目标检测算法主要分为两类：

1. **Two-Stage（两阶段）算法**  
   - 先生成可能存在物体的候选区域（Region Proposal）  
   - 再对候选区域进行分类与边框回归  
   - 典型算法：R-CNN、Fast R-CNN、Faster R-CNN  

2. **One-Stage（单阶段）算法**  
   - 直接在图像上输出最终检测结果  
   - 更快但精度可能略低  
   - 典型算法：YOLO、SSD  

---

#### **📌 目标检测的三个核心模块**

1. **检测窗口的选择（候选区域生成）**  
2. **图像特征提取**  
3. **分类器设计与边框回归**  

---

#### **📌 检测窗口的选择方法**

1. **滑动窗口法（Sliding Window）**  
   - 在图像上按照不同大小、间隔滑动窗口  
   - 利用训练好的分类器筛选高概率区域  
   - 通过 NMS（非极大值抑制）得到最终检测结果  

<div style="text-align:center">
  <img src="/assets/img/blogs/detection-intro/sliding_window.jpeg" alt="滑动窗口法" style="max-width:85%; height:auto;" data-zoomable>
</div>

2. **选择性搜索（Selective Search）**  
   - 为提高效率，不盲目搜索整张图像  
   - 流程：图像分割 → 基于相似度的合并 → 构建外接矩形框  
   - 最终生成一组感兴趣区域（ROI）  

<div style="text-align:center">
  <img src="/assets/img/blogs/detection-intro/selective_search.jpeg" alt="选择性搜索" style="max-width:85%; height:auto;" data-zoomable>
</div>

3. **区域提议网络（RPN, Region Proposal Network）**  
   - 通过卷积获得图像特征图  
   - 在每个特征图像素点创建多个候选框（anchor），不同尺度和纵横比  
   - 映射回原图即为候选窗口  
   - 利用卷积特征的语义化信息决定候选框位置与存在概率  
   - 相比滑动窗口和选择性搜索，RPN更高效、精确  

   **🎛️ RPN网络架构**

    - 基于共享卷积特征图，RPN分支为两个子任务：  
    1. **分类分支**：判断候选框是否包含目标  
    2. **回归分支**：微调候选框位置以更贴合目标边界  

    - 优势：  
    - 高层语义特征驱动的候选区域生成  
    - 高效替代传统手工设计的滑动窗口和选择性搜索  
    - 与 Fast/Faster R-CNN 等 Two-Stage 检测器完美集成  


---


> **总结**：目标检测的核心是“选区域+提特征+分类回归”。Two-Stage算法重视精度，One-Stage算法追求速度。随着深度学习发展，RPN和端到端的检测器（如Faster R-CNN、YOLO系列）大幅提升了检测效率和精度，为后续多模态与分割任务奠定基础。

---

<div class="post-navigation" style="margin-top:2rem; padding-top:1rem; border-top:1px solid #eaeaea;">
  <p style="margin-bottom:0.5rem; font-weight:600;">📖 系列文章导航</p>
  <ul style="list-style:none; padding-left:0;">
    <li>➡️ 下一篇：<a href="{{ '/blog/2024/clip' | relative_url }}">多模态视觉大模型解析——CLIP</a></li>
    <li>➡️ 下一篇：<a href="{{ '/blog/2024/twostage' | relative_url }}">目标检测Two-Stage算法解析</a></li>
  </ul>
</div>
