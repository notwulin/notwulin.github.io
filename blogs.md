---
layout: page
permalink: /blogs/index.html
title: 博客
page_class: editorial-page
side_nav_label: 导览
---

<p class="editorial-kicker">Blogs</p>
<h1 class="editorial-title">博客</h1>
<p class="editorial-deck">
  相比高频更新，我更在意内容是否值得在几年之后再回头看。这里会自动收录 <code>blogs/</code> 下的文章，并按元数据或目录分组。
</p>

{% assign blog_pages = site.pages | where_exp: "item", "item.path contains 'blogs/'" | where_exp: "item", "item.title" %}
{% assign sorted_blogs = blog_pages | sort: "date" | reverse %}
{% assign blog_total = sorted_blogs | size %}
{% capture blog_categories %}{% endcapture %}

{% for blog in sorted_blogs %}
  {% assign path_parts = blog.path | split: "/" %}
  {% assign folder_key = path_parts[1] | default: "" %}
  {% if blog.blog_category %}
    {% assign category_name = blog.blog_category %}
  {% elsif path_parts.size > 2 and folder_key != "" %}
    {% assign category_name = folder_key | replace: "-", " " | replace: "_", " " | capitalize %}
  {% else %}
    {% assign category_name = "随笔" %}
  {% endif %}
  {% capture category_token %}|{{ category_name }}|{% endcapture %}
  {% unless blog_categories contains category_token %}
    {% capture blog_categories %}{{ blog_categories }}{{ category_token }}{% endcapture %}
  {% endunless %}
{% endfor %}

{% if blog_total > 0 %}
<div class="blog-index">
  {% assign category_list = blog_categories | split: "|" %}
  {% assign category_index = 0 %}
  {% for category in category_list %}
    {% unless category == "" %}
    {% assign category_index = category_index | plus: 1 %}
    {% assign category_count = 0 %}
    {% for blog in sorted_blogs %}
      {% assign path_parts = blog.path | split: "/" %}
      {% assign folder_key = path_parts[1] | default: "" %}
      {% if blog.blog_category %}
        {% assign item_category = blog.blog_category %}
      {% elsif path_parts.size > 2 and folder_key != "" %}
        {% assign item_category = folder_key | replace: "-", " " | replace: "_", " " | capitalize %}
      {% else %}
        {% assign item_category = "随笔" %}
      {% endif %}
      {% if item_category == category %}
        {% assign category_count = category_count | plus: 1 %}
      {% endif %}
    {% endfor %}

    <section id="blog-category-{{ category_index }}" class="blog-category">
      <div class="blog-category-heading">
        <h2>{{ category }}</h2>
        <span>{{ category_count }} 篇</span>
      </div>

      <div class="blog-category-list">
        {% for blog in sorted_blogs %}
          {% assign path_parts = blog.path | split: "/" %}
          {% assign folder_key = path_parts[1] | default: "" %}
          {% if blog.blog_category %}
            {% assign item_category = blog.blog_category %}
          {% elsif path_parts.size > 2 and folder_key != "" %}
            {% assign item_category = folder_key | replace: "-", " " | replace: "_", " " | capitalize %}
          {% else %}
            {% assign item_category = "随笔" %}
          {% endif %}
          {% if item_category == category %}
          <article class="blog-entry">
            <p class="blog-entry-meta">
              {% if blog.date %}{{ blog.date | date: "%Y.%m.%d" }}{% else %}Undated{% endif %}
              {% if blog.tags %} · {{ blog.tags | join: " / " }}{% endif %}
            </p>
            <h3><a href="{{ blog.url | relative_url }}">{{ blog.title }}</a></h3>
            {% if blog.description %}
            <p class="blog-entry-summary">{{ blog.description }}</p>
            {% endif %}
            <p class="editorial-link-row">
              <a href="{{ blog.url | relative_url }}" class="editorial-link">Read Essay</a>
            </p>
          </article>
          {% endif %}
        {% endfor %}
      </div>
    </section>
    {% endunless %}
  {% endfor %}
</div>
{% else %}
<p class="editorial-note">还没有可展示的文章。给 <code>blogs/</code> 下的 Markdown 文件补上 front matter 后，它会自动出现在这里。</p>
{% endif %}

<section id="comments" class="editorial-section">
  <p class="editorial-section-kicker">Leave a Note</p>
  <h2>留言</h2>
  <p class="editorial-note">如果你读完之后有想交流的内容，可以直接在下面留言。</p>

  {% include disqus.html %}
</section>
