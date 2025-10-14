---
layout: page
title: 2024年·黄龙九寨
description: 和叽咪两天体验四季🤠
importance: 6
category: fun
related_publications: false
---

{% assign folder_name = page.path | split: '/' | last | split: '.' | first %}
{% assign cover_img = 'assets/img/life/' | append: page.category | append: '/' | append: folder_name | append: '/cover.jpg' %}


<!-- 1. 引入 GLightbox -->
<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/glightbox/dist/css/glightbox.min.css" />
<script src="https://cdn.jsdelivr.net/npm/glightbox/dist/js/glightbox.min.js"></script>

搁四川呆三年，总要来看一次秋天的九寨沟啊


    中午到黄龙九寨站，匆匆去往黄龙准备速通一波
    蓝蓝的水池遍布整座山
    这山这水这树，怎么看怎么像建模


<!-- 2. 图片网格 -->
<div class="gallery-grid">
  {% for i in (1..3) %}
    {% assign p = 'assets/img/life/' | append: page.category | append: '/' | append: folder_name | append: '/' | append: i | append: '.jpg' %}
    <a href="{{ p | relative_url }}" class="glightbox">
      <img src="{{ p | relative_url }}" loading="lazy">
    </a>
  {% endfor %}
</div>

    刚到山下还是大太阳的天气，突然开始飘雪
    缆车坐到山顶雪甚至越来越大，冻死
    想起来在山下被我嫌弃寄存在柜子的冲锋衣😭

    不过看到了好看的雪景，也算是因祸得福
    我叽咪简直是北海道大美女啊啊啊，jio美
    
<div class="gallery-grid">
  {% for i in (4..8) %}
    {% assign p = 'assets/img/life/' | append: page.category | append: '/' | append: folder_name | append: '/' | append: i | append: '.jpg' %}
    <a href="{{ p | relative_url }}" class="glightbox">
      <img src="{{ p | relative_url }}" loading="lazy">
    </a>
  {% endfor %}
</div>

    拍到了壁纸里会见到的雪山
    （叽咪拍的这个背影好有内味儿）
    
<div class="gallery-grid">
  {% for i in (9..11) %}
    {% assign p = 'assets/img/life/' | append: page.category | append: '/' | append: folder_name | append: '/' | append: i | append: '.jpg' %}
    <a href="{{ p | relative_url }}" class="glightbox">
      <img src="{{ p | relative_url }}" loading="lazy">
    </a>
  {% endfor %}
</div>

    鼎鼎大名的九寨！来了！
    还好把他安排在第二天，很幸运遇上了好天气，看到了五彩斑斓的秋和雪山
    
<div class="gallery-grid">
  {% for i in (12..16) %}
    {% assign p = 'assets/img/life/' | append: page.category | append: '/' | append: folder_name | append: '/' | append: i | append: '.jpg' %}
    <a href="{{ p | relative_url }}" class="glightbox">
      <img src="{{ p | relative_url }}" loading="lazy">
    </a>
  {% endfor %}
</div>

    有山有水的风景赛高！
    跟叽咪乱逛发现了p2这片无人之地
    这个机位简直绝了好吗💃
    
<div class="gallery-grid">
  {% for i in (17..19) %}
    {% assign p = 'assets/img/life/' | append: page.category | append: '/' | append: folder_name | append: '/' | append: i | append: '.jpg' %}
    <a href="{{ p | relative_url }}" class="glightbox">
      <img src="{{ p | relative_url }}" loading="lazy">
    </a>
  {% endfor %}
</div>

    西游记主题曲镜头取景瀑布
    据说是因为08年地震导致跟以前不太一样
    
<div class="gallery-grid">
  {% for i in (20..24) %}
    {% assign p = 'assets/img/life/' | append: page.category | append: '/' | append: folder_name | append: '/' | append: i | append: '.jpg' %}
    <a href="{{ p | relative_url }}" class="glightbox">
      <img src="{{ p | relative_url }}" loading="lazy">
    </a>
  {% endfor %}
</div>

    九寨秋景MVP——五花海
    蓝绿蓝绿的池水映着层林尽染的山野，真的一绝
    拍了简易版的藏族旅拍，感谢叽咪老师神来一手为我打造的美貌哈哈哈哈
    
<div class="gallery-grid">
  {% for i in (25..29) %}
    {% assign p = 'assets/img/life/' | append: page.category | append: '/' | append: folder_name | append: '/' | append: i | append: '.jpg' %}
    <a href="{{ p | relative_url }}" class="glightbox">
      <img src="{{ p | relative_url }}" loading="lazy">
    </a>
  {% endfor %}
</div>

    各种角度各种颜色的水
    这颜色竟然真实存在我的天
    九寨归来不看水不是空穴来风
    p1水下像有森林一样，肉眼看更震撼
    
<div class="gallery-grid">
  {% for i in (30..37) %}
    {% assign p = 'assets/img/life/' | append: page.category | append: '/' | append: folder_name | append: '/' | append: i | append: '.jpg' %}
    <a href="{{ p | relative_url }}" class="glightbox">
      <img src="{{ p | relative_url }}" loading="lazy">
    </a>
  {% endfor %}
</div>

特种兵的两天，实在是大饱眼福了，只是苦了我跟叽咪的腿

<style>
.gallery-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
  gap: 1rem;
  margin-bottom: 2rem;
}
.gallery-grid img {
  width: 100%;
  aspect-ratio: 4/3;
  object-fit: cover;
  border-radius: 0.5rem;
  box-shadow: 0 2px 6px rgba(0,0,0,0.15);
  cursor: pointer;
}
</style>
<script>
  const lightbox = GLightbox({
    selector: '.glightbox',
    touchNavigation: true,
    loop: true,
    zoomable: true
  });
</script>
