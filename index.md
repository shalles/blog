---
layout: page
title: shalles
tagline: 
---
{% include JB/setup %}

**写笔记的方式更适合我，后续请转到 [ https://note.shalles.org ](https://note.shalles.org)**

<div class="container">
    <div class="posts-box">
        <ul class="posts">
            {% for post in site.posts %}
            <li class="article-box">
                <div class="title">
                    <span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>
                </div>
                {% if post.img %}
                <div class="article-img">
                    <img src="{{ BASE_PATH }}{{post.img.url}}" alt="{{post.img.alt}}">
                </div>
                {% endif %}
                <div class="article-excerpt">
                    <span>{{ post.excerpt }}</span>
                </div>
                <a class="more" href="{{ BASE_PATH }}{{ post.url }}">read more ส้้้้้้้้้้้้้้้้้้</a>
                <div class="artible-catgory" title="{{ post.category }}">{{ post.category }}</div>
            </li>
            {% endfor %}
        </ul>
    </div>
    <div class="tools-box">
        <ul>
            <li class="profile"><img src="{{ BASE_PATH }}/assets/images/users/shalles512.jpg" alt=""></li>
            <li class="say">
                <h4>:</h4>
                <p>just do IT, finally, finish the game engining dream</p>
                <p>When the capacity can't meet your ambitions, please calm down to study</p>
            </li>
            <li class="recommend">
                <h4>:</h4>
                <p>1.生于毫末</p>
                <p>2.先利其器</p>
                <p>5.游戏人生</p>
            </li>
        </ul>
    </div>
</div>
