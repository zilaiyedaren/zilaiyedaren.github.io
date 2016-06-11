---
layout: page
title: 近期文章
permalink: /recent-article/
feature-img: "img/sample_feature_img_2.png"
---
<section id="archive">
  {%for post in site.posts %} 
    {% unless post.next %}
      <h3>{{ post.date | date: '%Y' }}</h3>
      <ul class="this">
    {% endunless %}
      <li>
        <time>{{ post.date | date:"%b %-d" }}</time>&nbsp;&nbsp;&nbsp;&nbsp;
        <a href="{{ post.url }}">{{ post.title }}
      </a>
      </li>
  {% endfor %}
  </ul>
</section>
