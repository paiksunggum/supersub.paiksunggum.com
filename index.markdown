---
layout: page
title: 표지
permalink: /
cover: true
---

<div class="report-cover">

  <p class="cover-client">{{ site.report.client }}</p>

  <h1 class="cover-title">{{ site.report.project_name }}</h1>

  <p class="cover-subtitle">{{ site.report.subtitle }}</p>

  <hr class="cover-rule">

  <dl class="cover-meta">
    <dt>개발 기간</dt>
    <dd>{{ site.report.period_start }} ~ {{ site.report.period_end }} <span class="cover-weeks">({{ site.report.period_weeks }})</span></dd>

    <dt>개발 인원</dt>
    <dd>{% for m in site.report.members %}{{ m }}{% unless forloop.last %} · {% endunless %}{% endfor %} <span class="cover-weeks">({{ site.report.members | size }}명)</span></dd>
  </dl>

  <p class="cover-next"><a href="{{ '/toc/' | relative_url }}">목차 →</a></p>

</div>
