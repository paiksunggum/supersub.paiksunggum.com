---
layout: page
title: 표지
permalink: /
cover: true
---

<div class="report-cover">

  <p class="cover-subtitle">{{ site.report.subtitle }}</p>

{% assign cover_title = site.report.project_name %}
{% for brk in site.report.title_breaks %}
  {% capture brk_html %}{{ brk }}<br>{% endcapture %}
  {% assign cover_title = cover_title | replace: brk, brk_html %}
{% endfor %}
  <h1 class="cover-title">{{ cover_title }}</h1>

  <p class="cover-title-en">{% for line in site.report.project_name_en %}{{ line }}{% unless forloop.last %}<br>{% endunless %}{% endfor %}</p>

  <hr class="cover-rule">

  <dl class="cover-meta">
    <dt>개발 기간</dt>
    <dd>{{ site.report.period_start }} ~ {{ site.report.period_end }} <span class="cover-weeks">({{ site.report.period_weeks }})</span></dd>

    <dt>팀명 : {{ site.report.team }}</dt>
    <dd>{% for m in site.report.members %}{{ m }}{% unless forloop.last %} · {% endunless %}{% endfor %} <span class="cover-weeks">({{ site.report.members | size }}명)</span></dd>

    <dt>깃허브 주소</dt>
    <dd><a href="{{ site.report.repo_url }}">{{ site.report.repo_url }}</a></dd>

    <dt>데모 사이트</dt>
    <dd><a href="{{ site.report.demo_url }}">{{ site.report.demo_url }}</a></dd>
  </dl>

  <p class="cover-next"><a href="{{ '/toc/' | relative_url }}">목차 →</a></p>

</div>
