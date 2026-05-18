---
layout: page
title: 博客
---

## 📝 文章列表

<div class="blog-list" style="margin-top: 24px;">
{% for post in site.posts %}
<a href="{{ post.url | relative_url }}" class="blog-card">
  <div class="blog-date">{{ post.date | date: "%Y-%m-%d" }}</div>
  <h3>{{ post.title }}</h3>
  {% if post.excerpt %}
  <p>{{ post.excerpt | strip_html | truncate: 200 }}</p>
  {% endif %}
  {% if post.tags %}
  <div class="blog-tags">
    {% for tag in post.tags %}
    <span>{{ tag }}</span>
    {% endfor %}
  </div>
  {% endif %}
</a>
{% else %}
<p style="text-align:center; color: var(--text-muted); padding: 80px 0;">
  <i class="fas fa-pen-fancy" style="font-size: 3rem; display: block; margin-bottom: 16px;"></i>
  还没有文章，敬请期待！
</p>
{% endfor %}
</div>
