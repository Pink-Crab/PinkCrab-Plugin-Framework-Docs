# Modules

A small selection of modules have been created for the plugin framework. These can not be used outside of the main package.

## Registerables

Allows for the creation and registartion of common WordPress fixtures such as Post Type, Taxonomies, Metaboxes, Meta Data and WP_Ajax calls.

> [Read docs](./registerables/README.md)   
> [View on github](https://github.com/Pink-Crab/Module__Registerables)

****

## Admin Pages

Allows for the creation of Admin Pages, Page Groups and includes the use of ACF option pages within groups.

> [Read docs](./admin-pages/README.md)   
> [View on github](https://github.com/Pink-Crab/Module__Admin_Pages)  

****

## BladeOne Provider

Allows the use of BladeOne as an engine for Renderable. Includes the additional BladeOne HTML addon.
```PHP
{{ $something }}
@form()
    @input(type="text" name="myform" value=$myvalue)
    @button(type="submit" value="Send")
@endform()
```
> [Read docs](./bladeone_provider/README.md)   
> [View on github](https://github.com/Pink-Crab/BladeOne_Provider)  

****

## Hook Subscriber

