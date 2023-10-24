---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
---

<table>
  <tr>
{% for link in site.data.navigation %}
  <td><a class="page-link" href="{{ link.url }}">{{ link.title }}</a></td>
{% endfor %}    
  </tr>
</table>

