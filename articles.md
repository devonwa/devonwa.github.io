---
layout: default
title: Articles
---

## Past articles

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