---
layout: default
title: Articles
---

<p><a href="/feed.xml" title="Articles RSS"><strong>RSS Feed <i class="fa fa-rss"></i></strong></a></p>

## Articles

<div>
    <ul id="post-list">
        {% for post in site.posts %}
            {% if post.type == "article" %}
            <li id="post-list-item">
                <h4>
                <small>
                    {{ post.date | date: "%Y-%m-%d" }}&nbsp;
                </small>
                <a href="{{ post.url }}">{{ post.title }}</a>
                </h4>
            </li>
            {% endif %}
        {% endfor %}
    </ul>
</div>
