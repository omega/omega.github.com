---
layout: default
---
<ul class="posts">
{% for post in site.posts limit:5 %}
    <li class="post">
        <a href="{{ post.url }}"><h1>{{ post.title }}</h1></a>
        <span class="date">
            <span class="day">{{ post.date | date: '%d' }}</span>
            <span class="month"><abbr>{{ post.date | date: '%b' | upcase }}</abbr></span>
            <span class="year">{{ post.date | date: '%Y' }}</span>
        </span>
        {{ post.content }}
    </li>
{% endfor %}
</ul>

