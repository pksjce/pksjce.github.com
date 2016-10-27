---
layout: page
title: Pavithra Kodmad
tagline: 
---
{% include JB/setup %}

<div style="height:100%">
  <div>
    JavaScripter, OSS newbie. Loves cats and webpack, not in equal measures.
  </div>

  <ul class="posts">
    {% for post in site.posts %}
      <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
  <div class='intro'>
  	
  </div>
</div>

