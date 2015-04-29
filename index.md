---
layout: page
title: shalles
tagline: 一切都是最好的安排
---
{% include JB/setup %}

<div class="container">
    <div class="posts-box">
        <ul class="posts">
            {% for post in site.posts %}
            <li class="article-box">
                <div class="title">
                    <span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}" title="{{ post.title }}">{{ post.title }}</a>
                </div>
                <div class="article-excerpt">
                    <span>{{ post.excerpt }}</span>
                </div>
                <a class="more" href="{{ BASE_PATH }}{{ post.url }}">read more ส้้้้้้้้้้้้้้้้้้</a>
                <div class="artible-catgory">{{ post.category }}</div>
            </li>
            {% endfor %}
        </ul>
    </div>
    <div class="tools-box">
        <ul>
            <li class="profile"><img src="{{ BASE_PATH }}/assets/images/users/shalles-qr.png" alt=""></li>
            <li class="say">
                <h4>shalles say:</h4>
                <p>just do IT, finally, finish the game engining dream</p>
                <p>When the capacity can't meet your ambitions, please calm down to study</p>
            </li>
            <li class="recommend">
                <h4>blog tenet:</h4>
                <p>1.合抱之木,生于毫末</p>
                <p>2.工欲善其事,必先利其器</p>
                <p>3.闻道有先后,树业有专攻</p>
                <p>4.博学者,善索引,博而有专</p>
                <p>5.这是一条很长的路,游戏人生</p>
            </li>
        </ul>
    </div>
</div>