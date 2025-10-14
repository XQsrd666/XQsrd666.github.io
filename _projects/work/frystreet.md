---
layout: page
title: 炸街车辆智能监测系统
description: 基于云-边协作与音视频多模态 AI，实现炸街（飙车）事件的实时检测、车辆定位与可视化执法支持。
importance: 1
category: work
related_publications: false
tags: [云-边协作, 音视频多模态, 实时定位, 可视化执法]
---

<!-- 引入 Lightbox -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/glightbox/dist/css/glightbox.min.css" />
<script src="https://cdn.jsdelivr.net/npm/glightbox/dist/js/glightbox.min.js"></script>

{% assign folder_name = page.path | split: '/' | last | split: '.' | first %}
{% assign img_dir = 'assets/img/projects/work/' | append: folder_name %}

<!-- 项目概览 -->
<div class="row mb-4">
  <div class="col-12">
    <p class="lead mb-4">
      本项目与<strong>成都市交通管理局</strong>合作，旨在解决城市道路"飙车/炸街"这一严重影响公共安全的问题。核心创新在于<strong>基于云-边协作架构，结合音视频多模态AI技术</strong>，实现低成本、高覆盖的炸街事件实时检测、精准定位与执法取证。
    </p>
    <p>我的核心工作聚焦于三个关键技术环节：</p>
    <ul>
      <li><strong>云-边协同检测架构设计：</strong>构建Jetson边缘端与云端推理的协同框架，边缘端负责RTSP采集、音频初筛与轻量视觉检测，云端进行深度学习精判与多模态融合分析，实现高效能计算资源分配。</li>
      <li><strong>音视频多模态融合算法：</strong>采用传统音频分析进行音频初筛，结合MFCC/Melspectrogram特征增强与CAM++/ECAPA-TDNN stacking模型，提升炸街识别的准确性与鲁棒性。</li>
      <li><strong>精准定位与可视化执法系统：</strong>基于TDOA声源定位技术与视觉目标跟踪，结合音视一致性对齐算法，实现炸街车辆的精准定位，并通过Django+Vue.js构建完整的可视化执法支持平台。</li>
    </ul>
    <p>
      该系统已在成都5个重点路段部署试点，累计检测炸街事件<strong>2098起</strong>，实现云端炸街判别准确率<strong>98.4%</strong>，车牌识别准确率<strong>99.5%</strong>，为交通噪音治理提供了智能化解决方案。
    </p>
  </div>
</div>

<!-- KPI 卡片 -->
<div class="row text-center mb-4 kpi-cards">
  {% assign kpi_list = 
    "云端炸街判别:98.4%,车牌识别:99.5%,试点事件数:2098,单帧时延:<25ms" | split: "," %}
  {% for kpi in kpi_list %}
    {% assign parts = kpi | split: ":" %}
    <div class="col-6 col-md-3 mb-3">
      <div class="card kpi-card h-100">
        <div class="card-body">
          <div class="kpi-title">{{ parts[0] }}</div>
          <div class="kpi-value">{{ parts[1] }}</div>
        </div>
      </div>
    </div>
  {% endfor %}
</div>

<hr>

<!-- 项目部署横图 -->
<div class="row mb-4">
  <div class="col-12">
    <h3>项目部署与成果概览</h3>
    <div class="text-center">
      <img src="{{ img_dir | append: '/deployment_overview.jpg' | relative_url }}" alt="项目部署横图" class="img-fluid mb-1" style="max-height:400px; object-fit:cover;">
      <div class="small text-muted mb-2">项目部署横图</div>
    </div>
    <p><strong>部署点位（示例）：</strong>科华中路、剑南大道、梓州大道、清江中路、泰华西路（5 点位）。</p>
    <p><strong>研发周期：</strong>约 6 个月（2023-10 ~ 2024-04）。</p>
    <p><strong>前期投入：</strong>约 ¥30 万（试点约 ¥3 万）。</p>
  </div>
</div>

<hr>

<!-- 系统架构 -->
<div class="row mb-4">
  <div class="col-12">
    <h3>系统架构概览</h3>
    <p>边端：负责 RTSP 采集、谱减/带通滤波降噪、短时能量双门限初筛与轻量视觉检测（Jetson 部署），处理完毕后将音视频及时空信息同步至云端。</p>
    <p>云端：深度学习精判、TDOA 校准定位、车牌识别、事件库与分析后台。</p>
    <div class="text-center">
      <img src="{{ img_dir | append: '/system_arch.jpg' | relative_url }}" alt="系统架构图" class="img-fluid mb-1" style="max-height:400px; object-fit:cover;">
      <div class="small text-muted mb-3">系统架构图</div>
    </div>
  </div>
</div>

<!-- 音视频结合与边端处理 -->
<div class="row mb-4">
  <div class="col-12">
    <h4>音视频结合与边端初步检测</h4>
    <p>边端在有限算力下进行视觉检测（RTSP 摄像头，约 1.72s/帧），初步识别炸街事件，减少上传数据量，准确率约 47.1%。</p>
    <div class="row mb-2 justify-content-center">
      {% assign imgs = "visual_effect.jpg|视觉效果,install.jpg|安装现场,realtime.jpg|实时处理" | split: "," %}
      <!-- 第一张图单独一行 -->
      {% assign parts = imgs[0] | split: "|" %}
      <div class="col-12 text-center mb-2">
        <img src="{{ img_dir | append: '/' | append: parts[0] | relative_url }}" 
            alt="{{ parts[1] }}" 
            class="img-fluid" 
            style="height:300px; object-fit:cover;">
        <div class="small text-muted mt-1">{{ parts[1] }}</div>
      </div>
    </div>
    <!-- 第二三张图并排，总宽度与第一张一致 -->
    <div class="row mb-2 justify-content-center" style="max-width:800px; margin:0 auto;">
      {% for i in (1..2) %}
        {% assign parts = imgs[i] | split: "|" %}
        <div class="col-auto mb-2 text-center">
          <img src="{{ img_dir | append: '/' | append: parts[0] | relative_url }}" 
              alt="{{ parts[1] }}" 
              class="img-fluid" 
              style="height:200px; object-fit:cover;">
          <div class="small text-muted mt-1">{{ parts[1] }}</div>
        </div>
      {% endfor %}
    </div>

  </div>
</div>

<!-- 云端二次精确炸街检测 -->
<div class="row mb-4">
  <div class="col-12">
    <h4>云端二次精确炸街检测</h4>
    <p>结合深度学习提升识别准确率：</p>
    <ul>
      <li>特征提取：MFCC、Melspectrogram 与 Fusion 混合特征</li>
      <li>特征增强：SpecAug + SpeedPerturb</li>
      <li>炸街识别：CAM++ + ECAPA-TDNN Stacking，提高小数据集下识别鲁棒性</li>
    </ul>
    <div class="text-center">
      <img src="{{ img_dir | append: '/nn_flow.jpg' | relative_url }}" alt="神经网络流程" class="img-fluid mb-1" style="max-height:400px; object-fit:cover;">
      <div class="small text-muted mb-3">神经网络流程</div>
    </div>
  </div>
</div>

<!-- 炸街车辆定位 -->
<div class="row mb-4">
  <div class="col-12">
    <h4>炸街车辆定位</h4>
    <ul>
      <li>目标过滤：YOLOv8 识别炸街车辆特征</li>
      <li>速度过滤：连续帧光流计算 + 速度阈值判断</li>
      <li>车道判别：TDOA 声源定位 + 蜂鸣器校准，结合 FFT 和音频相似度计算车道概率分布</li>
      <li>车牌识别：YOLOv8 + CRNN，存入数据库</li>
    </ul>
    <div class="row mb-2 justify-content-center">
      {% assign imgs = "error_dist.jpg|概率误差分布,position.jpg|定位探测,vehicle_loc.jpg|炸街车辆定位" | split: "," %}
      <!-- 前两张图并排 -->
      {% for img_pair in imgs %}
        {% assign parts = img_pair | split: "|" %}
        {% if forloop.index0 < 2 %}
          <div class="col-auto mb-2 text-center">
            <img src="{{ img_dir | append: '/' | append: parts[0] | relative_url }}" 
                alt="{{ parts[1] }}" 
                class="img-fluid" 
                style="height:200px; object-fit:cover;">
            <div class="small text-muted mt-1">{{ parts[1] }}</div>
          </div>
        {% endif %}
      {% endfor %}
    </div>
    <!-- 第三张图单独一行 -->
    <div class="row mb-2 justify-content-center">
      {% assign parts = imgs[2] | split: "|" %}
      <div class="col-12 text-center">
        <img src="{{ img_dir | append: '/' | append: parts[0] | relative_url }}" 
            alt="{{ parts[1] }}" 
            class="img-fluid" 
            style="height:200px; object-fit:cover;">
        <div class="small text-muted mt-1">{{ parts[1] }}</div>
      </div>
    </div>

  </div>
</div>

<!-- 实时检测综合系统 -->
<div class="row mb-4">
  <div class="col-12">
    <h4>实时检测综合系统</h4>
    <p>前后端采用 Vue.js + Django，提供可视化综合管理服务：</p>
    <ul>
      <li>精准炸街识别（边缘短时能量 + 云端深度学习）</li>
      <li>炸街车辆定位（YOLOv8 + 光流 + TDOA + CRNN 识别车牌）</li>
      <li>搜索功能：地点、设备、时间或事件编号</li>
      <li>地图显示：点位模式 / 热力图模式</li>
      <li>近期事件列表：发生地点与时间，可点击查看详细视频</li>
    </ul>
    <div class="text-center">
      <img src="{{ img_dir | append: '/visualize_platform.jpg' | relative_url }}" alt="系统功能示意图" class="img-fluid mb-1" style="max-height:400px; object-fit:cover;">
      <div class="small text-muted mb-3">系统功能示意图</div>
    </div>
  </div>
</div>

<!-- 技术栈 -->
<div class="row mb-4">
  <div class="col-12">
    <h4>技术栈</h4>
    <p>
      <span class="badge" style="background-color:var(--global-theme-color); color:var(--global-card-bg-color); margin-right:.25rem">Jetson</span>
      <span class="badge" style="background-color:var(--global-theme-color); color:var(--global-card-bg-color); margin-right:.25rem">DeepStream</span>
      <span class="badge" style="background-color:var(--global-theme-color); color:var(--global-card-bg-color); margin-right:.25rem">YOLOv8</span>
      <span class="badge" style="background-color:var(--global-theme-color); color:var(--global-card-bg-color); margin-right:.25rem">CRNN / LPRNet</span>
      <span class="badge" style="background-color:var(--global-theme-color); color:var(--global-card-bg-color); margin-right:.25rem">CAM++ / ECAPA-TDNN</span>
      <span class="badge" style="background-color:var(--global-theme-color); color:var(--global-card-bg-color); margin-right:.25rem">Django</span>
      <span class="badge" style="background-color:var(--global-theme-color); color:var(--global-card-bg-color); margin-right:.25rem">Vue.js</span>
      <span class="badge" style="background-color:var(--global-theme-color); color:var(--global-card-bg-color); margin-right:.25rem">高德地图 JS API</span>
    </p>
  </div>
</div>

<hr>
<!-- 量化结果 -->
<!-- <div class="row mb-4">
  <div class="col-12">
    <h3>量化结果（试点实测）</h3>
    <ul>
      <li>试点检测到事件：<strong>2098 起（2023-10 ~ 2024-04）</strong></li>
      <li>云端炸街判别准确率：<strong>98.4%</strong></li>
      <li>车牌识别准确率：<strong>99.5%</strong></li>
      <li>音视频综合定位：
        <strong>单车 98.3%</strong> / <strong>多车 83.6%</strong>
      </li>
      <li>音频模型误报率：<strong>12%</strong>，召回率：<strong>93%</strong></li>
      <li>系统平均炸街检测耗时：<strong>≈3.5 秒</strong></li>
    </ul>
  </div>
</div>


<hr> -->

<!-- 结论与下一步 -->
<div class="row mb-4">
  <div class="col-12">
    <h3>结论与下一步计划</h3>
    <p>系统在试点已验证高准确率与实时性，下一步侧重于：</p>
    <ol>
      <li>扩大点位覆盖并做跨路况/跨城市适配；</li>
      <li>提升多车场景的定位鲁棒性；</li>
      <li>商业化与运维工具：自动化升级脚本、模型持续学习流水线、监控告警系统；</li>
      <li>数据合规与隐私评估（对接交管局流程与法规）。</li>
    </ol>
  </div>
</div>

<!-- 样式优化 -->
<style>
/* 图片统一样式 */
img.img-fluid {
  max-width: 100%;
  height: auto;
  display: block;
}
.text-center img {
  display: inline-block;
  margin: 0 auto;
}

/* Badge风格统一 */
.badge {
  font-size: 0.75rem;
  padding: 0.35em 0.6em;
}
</style>

<!-- 初始化 GLightbox -->
<script>
document.addEventListener('DOMContentLoaded', function () {
  if (typeof GLightbox !== 'undefined') {
    GLightbox({ selector: '.glightbox', touchNavigation:true, loop:true });
  }
});
</script>
