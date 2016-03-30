---
layout: default
title: Articles
---

<p><a href="/feed.xml" title="Articles RSS"><strong><i class="fa fa-rss"></i> RSS Feed</strong></a></p>

## Articles

<div>
    <ul id="post-list">
        {% for post in site.posts %}
            {% if post.type == "article" %}
            <li id="post-list-item">
                {{ post.date | date: "%Y-%m-%d" }}&nbsp;
                <a href="{{ post.url }}">{{ post.title }}</a>
            </li>
            {% endif %}
        {% endfor %}
    </ul>
</div>
