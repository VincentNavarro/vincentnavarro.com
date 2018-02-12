---
title: "Blog"
layout: "wrapper"
permalink: "/blog"
tags: link
---
<div id="blog">
   <h2>Blog</h2>
   {% for post in site.posts %}
         <a href="{{ post.url }}">{{ post.title }}</a>
         <br>
         <span class="light">
            <i>
               {{ post.date | date: "%b %Y" }} •
               {% assign words = post.content | number_of_words %}
               {% if words < 270 %}
                  1 min read
               {% else %}
                  {{ words | divided_by:135 }} min read
               {% endif %}
            </i>
         </span>
         <br /><br />
   {% endfor %}
</div>
