---
title: オンラインでホスティングされている手順
permalink: index.html
layout: home
---

# <a name="data-analyst-exercises"></a>データ アナリスト演習

DP-500: Microsoft Azure と Microsoft Power BI を使用した Enterprise-Scale Analytics ソリューションの設計および実装

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %}
| ILT モジュール | ラボ |
| --- | --- | 
{% for activity in labs %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

<!--

## Demos

{% assign demos = site.pages | where_exp:"page", "page.url contains '/Instructions/Demos'" %}
| Module | Demo |
| --- | --- | 
{% for activity in demos  %}| {{ activity.demo.module }} | [{{ activity.demo.title }}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}
 
-->