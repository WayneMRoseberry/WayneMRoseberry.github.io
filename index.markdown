---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---
{% for link in site.data.navigation %}
  <a class="page-link" href="{{ link.url }}">{{ link.title }}</a>
{% endfor %}
