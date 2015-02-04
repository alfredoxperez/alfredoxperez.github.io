---
layout: default
---

<div class="post-content-box">
<div class="post-title">
    <h1>{{ page.title }}</h1>
    {% include post-header.md %}
</div>

  {{ content }}

</div>


<div class="comments">
  {% include disqus.md%}
</div>


<br/>
<div class="post-footer">
  {% include post-footer.md %}
</div>

       

