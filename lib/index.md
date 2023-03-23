# Libs

The following is a collection of Libraries which while not been explicitly created for Perique, can be used or are often used as dependencies for Perique Modules

{% for lib in site.data.libraries %}
## {{ lib.title }}

{{ lib.description }}  
[Docs]({{ lib.url }}) \| [Github]({{ lib.github }}) 
{% endfor %}
