---
layout: default
---
<ul class="posts">
{% for post in site.posts limit:5 %}
    <li class="post">
        {% if post.title %}<a href="{{ post.url }}"><h1>{{ post.title }}</h1></a>{% endif %}
        <span class="date">{{ post.date | date: '%d %b %Y' }}</span>
        {{ post.content }}
    </li>
{% endfor %}
</ul>

