# Module 

Collection of extension modules for Perique.


{% for mod in site.data.modules %}
## {{ mod.title }}

{{ mod.description }}  
[Docs]({{ mod.url }}) \| [Github]({{ mod.github }}) 
{% endfor %}
