---
layout: page
title: Pavithra Kodmad
tagline: 
---
{% include JB/setup %}

<div style="height:100%">
  <div>
    Client side has undergone a large revolution of sorts in terms of its importance. I'm here, riding the wave!
  </div>

  <ul class="posts">
    {% for post in site.posts %}
      <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
  <div class='intro'>
  	
  </div>
</div>

