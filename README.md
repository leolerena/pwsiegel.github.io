<blockquote>
<p>*"Everything in moderation, including moderation."*</p>
<p align="right">-Oscar Wilde</p>
</blockquote>

# Welcome

I am Paul Siegel, a mathematician and data scientist in New York City.
This site is home to my blog and whatever else I feel like sharing with the world.

## Latest blog posts

{% assign live = site.posts | where:"tags", "live" %}

<ul>
   {% for post in live limit:5 %}
      <li>
         <a href="{{ post.url }}">{{ post.title }}</a> {{ site[post.level] }} - {{ post.date | date: '%B %d, %Y' }}
         <p>{{ post.abstract }}</p>
      </li>
   {% endfor %}
</ul>
