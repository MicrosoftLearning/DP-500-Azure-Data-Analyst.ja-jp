---
title: オンラインでホスティングされている手順
permalink: index.html
layout: home
---

# データ アナリスト演習

これらの演習では、Microsoft コース [DP-500: Microsoft Azure と Microsoft Power BI を使用した Enterprise-Scale Analytics ソリューションの設計および実装](https://docs.microsoft.com/training/courses/dp-500t00)をサポートしています

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/labs'" %}
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
