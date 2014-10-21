---
layout: default
title: Blog
---

# Blog

My blog contains lengthy articles to communicate ideas, but it also has short technical notes and reference lists that I write for myself and find useful. Feel free to comment on any.

<div><hr></div>

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


## Notes

<div>
    <ul id="post-list">
        {% for post in site.posts %}
            {% if post.type == "note" %}
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
