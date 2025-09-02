---
layout: page
title: Blog
icon: fas fa-pen-nib
order: 5
permalink: /blog/
---

<!-- Blog Posts List -->
<section style="max-width: 800px; margin: 0 auto; font-size: 1rem; line-height: 1.6;">
  <ul style="list-style-type:none; padding-left:0;">
  {% for post in site.posts %}
    {% unless post.categories contains "Projects" %}
      <li style="margin-bottom: 2rem; border-bottom: 1px solid #eee; padding-bottom: 1rem;">
        <p style="font-size:0.9rem; color:#999;">{{ post.date | date: "%B %d, %Y" }}</p>
        <h2 style="margin-bottom: 0.3rem;">
          <a href="{{ post.url | relative_url }}" style="color:#007acc; text-decoration:none;">{{ post.title }}</a>
        </h2>
        {% if post.excerpt %}
          <p>{{ post.excerpt | strip_html | truncatewords: 30 }}</p>
        {% endif %}
      </li>
    {% endunless %}
  {% endfor %}
  </ul>
</section>