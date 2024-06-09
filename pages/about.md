---
layout: page
title: About
description: 闲来总觉地方小， 周身一合天下宽
keywords: Chaojie Men, 门超杰
comments: true
menu: 关于
permalink: /about/
---

我是门超杰， 

行至今日，未停求索，

坚信有归期必有归处，

留下足迹，努力前行。

## 联系

<ul>
{% for website in site.data.social %}
<li>{{website.sitename }}：<a href="{{ website.url }}" target="_blank">@{{ website.name }}</a></li>
{% endfor %}
{% if site.url contains 'mazhuang.org' %}
<li>
微信公众号：<br />
<img style="height:192px;width:192px;border:1px solid lightgrey;" src="{{ site.url }}/assets/images/qrcode.jpg" alt="这是什么" />
</li>
{% endif %}
</ul>


## Skill Keywords

{% for skill in site.data.skills %}
### {{ skill.name }}
<div class="btn-inline">
{% for keyword in skill.keywords %}
<button class="btn btn-outline" type="button">{{ keyword }}</button>
{% endfor %}
</div>
{% endfor %}
