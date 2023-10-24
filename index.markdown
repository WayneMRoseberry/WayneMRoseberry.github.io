---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

Site Links:
-----------

{% for link in site.data.links %}
  <p>{{ link.desc } : <a href="{{ link.url }}">{{link.title}}</a></p>
{% endfor %}

