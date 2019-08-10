---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: page
---

{% for post in site.posts limit:1 %}
<div class="post">

  <header class="post-header">
    <a href="{{ post.url | prepend: site.baseurl }}">
        <h1 class="post-title">{{ post.title }}</h1>
    </a>
    <p class="post-meta">{{ post.date | date: "%b %-d, %Y" }}{% if post.author %} • {{ post.author }}{% endif %}{% if post.meta %} • {{ post.meta }}{% endif %}</p>
  </header>

  <article class="post-content">
    {{ post.content }}

    {% include post_ending_cta.html %}
  </article>

</div>

{% endfor %}
<h2>Previous posts</h2>
  <ul class="post-list">
{% for post in site.posts offset:1 %}

      <li>
          {% for tag in post.tags %}
          <i class="fa {{ tag }} fa-fw"></i>
          {% endfor %}
          <a class="post-link" href="{{ post.url | prepend: site.baseurl }}">{{ post.title }}</a>
        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
      </li>
{% endfor %}

  </ul>